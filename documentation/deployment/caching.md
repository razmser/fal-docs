> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Caching

> How fal's multi-layer caching system reduces cold start times by caching Docker images, model weights, and compiled artifacts.

Every time a new [runner](runners.md) starts, it needs Docker image layers, model weights, and potentially compiled model caches before it can serve requests. Without caching, each runner would download everything from scratch, leading to cold starts that can take minutes for large models. fal's caching system stores these artifacts at multiple levels so that subsequent runners start faster, and cold start times improve progressively as your app serves traffic.

Docker layers and files on [`/data`](../development/use-persistent-storage.md) are cached automatically. You don't need to configure anything. Compiled model caches (PyTorch Inductor) require explicit use of the [`synchronized_inductor_cache()`](../serverless/optimizations/optimize-startup-with-compiled-caches.md) API. Understanding the cache layers helps you reason about why cold starts improve over time and how to structure your app for the best startup performance. For strategies to reduce cold starts further, see [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md).

## Cache Layers

Docker images and `/data` storage use different caching mechanisms.

### `/data` Storage Cache

Files on [`/data`](../development/use-persistent-storage.md) (model weights, compiled caches, any files your app writes) go through a three-layer cache:

| Cache Layer | Speed | Scope | Use Case |
| :- | :- | :- | :- |
| **Local Node Cache** | 10-15 GB/s | Same physical machine | RAID 5 NVMe drives on the node |
| **Distributed Cache** | 6-8 GB/s | Same datacenter/region | 100 Gbps network across all servers |
| **Object Store** | 1.5-8 GB/s | Global | Backing store for all files |

When a runner reads a file from `/data`, it checks the local node cache first. If the file isn't found locally, it checks the distributed datacenter cache. If that also misses, it fetches from the object store and populates both caches on the way back. This means the first runner in a region pays the full download cost, but every runner after it benefits from progressively faster access.

### Docker Image Cache

Each node keeps Docker images in a local image store, separate from the `/data` cache. When a runner starts, the scheduler prefers nodes that already have your image. This preference is not a guarantee. The scheduler also considers GPU availability and region, so a runner can start on a node that must pull the image first. When the node does not have the image, it pulls the image from the container registry.

After you deploy a new revision, fal can pull its image in advance onto nodes in the datacenters where your app runs. fal prefers nodes that have the image of the previous revision, because these nodes already have most of the layers.

When a node needs disk space, it removes the least recently used images first. If your app does not run for some time, a node can pull its image again.

fal keeps a BuildKit registry cache for each app. When you change a small part of your Dockerfile, fal rebuilds only the affected layers. When you deploy without changes to the image, fal uses the existing image and does not build again. To force a rebuild, pass `--no-cache` to `fal deploy`.

The build cache is separate for each [environment](manage-environments.md). For example, you can deploy a commit to a staging environment and then to the `main` environment. The `main` build does not use the layers from the staging build.

## What Gets Cached

**Docker Image Layers**: Docker images are split into layers, and each layer is cached independently. If you change a single dependency in your Dockerfile, fal rebuilds only the affected layers. A node that has the previous image pulls only the changed layers. See [Optimize Container Images](../serverless/optimizations/optimize-container-images.md) for tips on structuring your Dockerfile for better layer caching.

**Model Weights**: files downloaded to `/data` are automatically cached across runners through the three-layer `/data` cache described above. This includes Hugging Face models (cached at `/data/.cache/huggingface` via the `HF_HOME` [environment variable](../development/environment-variables.md)), weights downloaded with [`download_model_weights()`](../development/working-with-files.md#downloading-model-weights), and any other files you write to `/data`. The first runner downloads the weights; subsequent runners read from the local or distributed cache.

**Compiled Model Caches**: PyTorch Inductor compiled models and other JIT compilation artifacts. If you use [`torch.compile()`](https://pytorch.org/docs/stable/generated/torch.compile.html), you can persist and share the resulting compiled kernels across runners using [`synchronized_inductor_cache()`](../serverless/optimizations/optimize-startup-with-compiled-caches.md). This stores the cache on `/data` (at `/data/inductor-caches/<GPU_TYPE>/`), where it benefits from the same three-layer cache. See [Compiled Kernel Caching](../serverless/optimizations/optimize-startup-with-compiled-caches.md) for setup instructions.

## How Caches Warm Up

Caches warm progressively as your application serves traffic:

1. **First runner**: pulls the Docker image from the registry, unless fal pulled it in advance after the deploy. Downloads model weights from the object store. Populates the local and distributed `/data` caches. This is the slowest cold start.
2. **Second runner on the same node**: the Docker image is already in the node's image store, and model weights are in the local NVMe cache. Cold start is significantly faster.
3. **Runners on other nodes**: model weights are served from the distributed `/data` cache. The scheduler prefers nodes that already have the Docker image. If the runner starts on a node without the image, the node pulls it from the registry.
4. **Over time**: as more nodes serve your app, caches spread across the region. Cold starts converge to the time it takes to run `setup()` with all files already available locally.

> [!NOTE]
>
> Caches are not permanent. Local caches can be evicted when nodes are recycled or need disk space. If your app hasn't had traffic for a while, the first cold start after a quiet period can be slower as caches are repopulated.
