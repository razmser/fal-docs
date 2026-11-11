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
> - [Optimizing Cold Starts](../optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Runner Analytics

> Debug individual runners with cold start stage breakdowns, live telemetry, per-runner logs, and shell access.

The Runners page gives you infrastructure-level visibility into the machines serving your app. Where [App Analytics](app-analytics.md) tells you *what* your traffic and latency look like, Runner Analytics tells you *why* -- which stage of startup is slow, how a runner is using its resources, and what it logged before it failed. It is the fastest way to go from "some requests have a long cold start" to the specific runner and stage responsible.

For background on what runners are and their lifecycle states, see [Understanding Runners](../../deployment/runners.md).

## Runner States and Cold Start Stages

The Runners page opens with a breakdown of your runners by state (running, pending, docker pull, draining, and more). Each runner row also exposes the individual stages that make up its cold start, so you can see exactly where startup time goes:

* **Pending** -- time to allocate a runner from fal infrastructure. High pending time on scarce or bursting GPU types (for example, B200s) points to capacity pressure rather than your code.
* **Docker pull** -- time to pull the container image. Rows indicate whether the image was cached or required a full pull.
* **Setup** -- time spent in your `setup()` function, typically loading model weights onto the GPU. This is often the largest contributor to cold starts.
* **Cold start** -- the total startup time across all stages.

You can sort runners by cold start time to immediately surface the slowest ones, then drill into any single runner for detail.

## The Runner Side Sheet

Clicking a runner opens a side sheet with everything you need to debug it:

* **Cold start timeline** -- a stage-by-stage visualization of the runner's startup, making it obvious whether the delay came from allocation, docker pull, or your setup code.
* **Telemetry** -- CPU, memory, GPU utilization, and VRAM usage over time, each as its own chart. GPU utilization shows the average compute load across the runner's GPUs, while VRAM shows GPU memory usage -- together they let you tell a compute-bound runner apart from a memory-bound one, and spot resource exhaustion (for example, an out-of-memory condition).
* **Logs** -- the full log timeline filtered to that runner ID, from spin-up through request execution, so errors like CUDA out-of-memory are easy to find in context.
* **Connect** -- SSH into an active runner with [`fal runners shell`](../../../api-reference/cli/runners.md#shell) to inspect the live environment (for example, run `nvidia-smi` to check driver specs and VRAM in real time). Available for active runners only.
* **Termination reason and error type** -- see why a runner was terminated (for example, a runner error) directly in the header.

## Managing Runners

From the runner list or side sheet you can act on runners directly -- use the actions menu to **gracefully stop** a runner (letting it finish its current request) or **kill** it immediately. See [Monitor Runners](monitor-performance.md#cli) and the [`fal runners`](../../../api-reference/cli/runners.md) reference for the equivalent CLI commands.

## Reducing Cold Starts

If Runner Analytics shows consistently high pending or setup times, that is a signal to optimize. Reserved capacity eliminates pending and setup time for a guaranteed pool of GPUs, giving predictable performance and economics. For setup-time reductions, see [Optimize Cold Starts](../optimizations/optimize-cold-starts.md), [compiled caches](../optimizations/optimize-startup-with-compiled-caches.md), [FlashPack](../optimizations/flashpack.md), and scaling parameters like the concurrency buffer in [Scale Your Application](../../deployment/scale-your-application.md).

> [card] [Understanding Runners](../../deployment/runners.md)
>
> Runner lifecycle, states, and caching

> [card] [Optimize Cold Starts](../optimizations/optimize-cold-starts.md)
>
> Techniques to reduce startup latency

> [card] [App Analytics](app-analytics.md)
>
> Request metrics, latency percentiles, and cold boot rate

> [card] [Logs](logs.md)
>
> Timeline, filtering, and sharing for runner and request logs
