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

# App Analytics

> Monitor your application's performance with real-time metrics and detailed analytics.

Every deployed app on fal comes with built-in analytics accessible from the [dashboard](https://fal.ai/dashboard/apps). You can see request volume, latency, error rates, and runner utilization at a glance, then drill into individual requests or runners to understand what happened.

The analytics are split into two views. The requests view answers questions about traffic and performance: how many requests are coming in, how fast they complete, which endpoints are slowest, and which requests failed. The runners view answers questions about infrastructure: how runner time is split between setup, inference, and idle, how deep the queue is, and whether you have the right scaling parameters.

## Requests

Navigate to **Dashboard > Apps > \[your-app] > Analytics** to see request metrics. You can filter by endpoint, date range, and view mode (your requests vs all requests to your app).

The key metrics to watch are request throughput, latency percentiles (P50, P90), and error rate. If latency is higher than expected, drill into individual requests to see a full breakdown from submission to completion, including inputs, outputs, and [logs](../../development/logging.md). If errors are spiking, use [Error Analytics](error-analytics.md) for a detailed breakdown by status code and error type.

## Runners

The runners view shows how your infrastructure is performing. Use it to understand whether your [scaling parameters](../../deployment/scale-your-application.md) are tuned correctly.

If runners are spending most of their time in setup, your cold starts are too long. Look at [Optimizing Cold Starts](../optimizations/optimize-cold-starts.md) for strategies. If runners are mostly idle, your `keep_alive` or `min_concurrency` may be too high. If the queue is consistently deep, you may need to increase `max_concurrency` or add [concurrency buffer](../../deployment/scale-your-application.md).

You can also inspect individual runners to see their CPU, memory, GPU, and VRAM usage, and jump directly to their [logs](../../development/logging.md) for debugging.

## Metrics Definitions

### Request Timing

Every request passes through several stages. The dashboard breaks these into two key charts: **Request Startup** (time before processing begins) and **Request Duration** (time spent processing).

| Metric | What it measures |
| - | - |
| **Request Startup** | Time from submission until the request starts processing on a runner. For queued requests, this includes queue wait time and runner acquisition. If a cold start is needed, the startup time includes waiting for the new runner's `setup()` to complete. This is the metric most affected by [scaling parameters](../../deployment/scale-your-application.md). |
| **Request Duration** | Time the request spent processing once it reached a runner. This is the actual execution time of your endpoint handler. Does not include queue wait or cold start time. |
| **Time to First Byte** | Time from when the request was sent to the runner until the first byte of the response was received. Useful for [streaming endpoints](../../development/streaming.md) where you want to measure how quickly the user sees initial output. |
| **Latency Percentiles (P50, P75, P90)** | Both Request Startup and Request Duration are shown at the 50th, 75th, and 90th percentile. P90 means 90% of requests completed faster than this value. Use P90 to understand worst-case performance for most users. |

### Runner Timing

Runner time is split into three categories. The dashboard shows these as a stacked area chart over time.

| Metric | What it measures |
| - | - |
| **Startup Time** | Time spent in the SETUP state, running your `setup()` method. This is your cold start cost. Appears once per runner lifecycle, not per request. Reduce it with [FlashPack](../optimizations/flashpack.md), [compiled caches](../optimizations/optimize-startup-with-compiled-caches.md), or by optimizing your `setup()` code. |
| **Execution Time** | Time the runner spent actively processing requests (in the RUNNING state). This is the productive time you want to maximize. |
| **Idle Time** | Time the runner was alive but not processing any requests (in the IDLE state). Several scaling parameters affect idle time. [`keep_alive`](../../deployment/scale-your-application.md) controls how long a runner stays alive after its last request before being terminated. [`min_concurrency`](../../deployment/scale-your-application.md) keeps runners permanently warm even with no traffic, which means they accumulate idle time continuously. [`concurrency_buffer`](../../deployment/scale-your-application.md) maintains extra runners above current demand, so those buffer runners are idle until traffic increases. [`scaling_delay`](../../deployment/scale-your-application.md) makes the autoscaler wait before spinning up new runners, which can reduce idle time by reusing existing runners instead of creating new ones that sit idle. |
| **Runner Utilization** | Execution time as a percentage of total runner time (startup + execution + idle). Higher utilization means your runners are spending more time doing useful work. If utilization is low, look at which idle-time factor is the largest contributor: reduce `keep_alive` if runners linger too long after traffic stops, reduce `min_concurrency` if you have more warm runners than you need, or increase `scaling_delay` to encourage runner reuse. |

### Queue and Concurrency

| Metric | What it measures |
| - | - |
| **Queue Size** | Number of requests currently waiting in the queue for an available runner. A consistently growing queue means you need more runners (increase `max_concurrency` or `concurrency_buffer`). |
| **Concurrent Requests** | Number of requests being actively processed across all runners at a given moment. |
| **Error Rate** | Percentage of requests that returned a 4XX or 5XX status code over the selected time period. |
