> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../quickstart.md) and the [Model APIs overview](../../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Optimizing Cold Starts

> Understanding cold starts and how to reduce them.

## Cold Starts vs Warm Starts

A **cold start** occurs when a new runner needs to be created from scratch. The runner goes through `PENDING` → `SETUP` → `IDLE` (or `PENDING` → `DOCKER_PULL` → `SETUP` → `IDLE` if the Docker image isn't [cached](../../deployment/caching.md)) before it can serve requests.

A **warm start** occurs when an existing IDLE runner is reused to handle a new request: `IDLE` → `RUNNING`.

### What Triggers Cold Starts

* No warm runners available (all busy or expired)
* Traffic spike exceeds warm runner capacity
* First deployment
* Runners expired during low traffic periods

### Factors Affecting Cold Start Duration

* **Image size**: Larger Docker images take longer to pull
* **Model size**: Larger models take longer to download and load
* **Setup complexity**: Complex initialization in `setup()` adds time
* **Cache state**: First runs are slower, subsequent runs benefit from caching
* **Hardware availability**: GPU availability varies by region and time

## How to Reduce Cold Starts

Each of these strategies targets a different phase of the cold start:

> [card] [Scaling Parameters](cold-start-scaling.md)
>
> Keep warm runners available with keep\_alive, min\_concurrency, and buffers

> [card] [Container Images](optimize-container-images.md)
>
> Reduce image size for faster pulls with multi-stage builds and smaller base images

> [card] [Compiled Caches](optimize-startup-with-compiled-caches.md)
>
> Cache compiled kernels to speed up setup() across runners

> [card] [Persistent Storage](../../development/use-persistent-storage.md)
>
> Download models to /data for automatic caching between runners
