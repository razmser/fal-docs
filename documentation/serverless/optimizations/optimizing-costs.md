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

# Optimizing Costs

> Lower your fal Serverless bill. Reduce idle time with keep_alive and min_concurrency, increase runner utilization, shorten setup, and right-size machines.

Since Serverless billing covers the entire runner lifetime, the most effective cost optimizations focus on reducing idle time, maximizing utilization, and minimizing setup duration.

## Reduce Idle Time

Idle time is the single biggest cost lever. Every second a runner is alive but not processing requests is billed.

### Tune `keep_alive`

The `keep_alive` parameter controls how long a runner stays alive after finishing its last request. Lower values reduce idle billing but increase cold starts.

```python theme={null}
class MyApp(fal.App, keep_alive=30):  # 30 seconds (default: 60)
    ...
```

| `keep_alive` | Trade-off |
| - | - |
| High (300s+) | Low latency, higher idle costs |
| Medium (30-60s) | Balanced for moderate traffic |
| Low (10-30s) | Cost-efficient for bursty workloads |

### Set `min_concurrency` carefully

Each runner in `min_concurrency` runs continuously and is billed 24/7. Only use it for latency-critical applications that cannot tolerate cold starts.

```python theme={null}
class MyApp(fal.App, min_concurrency=0):  # No always-on runners
    ...
```

## Maximize Runner Utilization

Runners that process one request at a time have idle gaps between sequential requests. Multiplexing eliminates these gaps.

### Use multiplexing

`max_multiplexing` allows a single runner to process multiple requests concurrently, filling idle time while one request waits on I/O.

```python theme={null}
class MyApp(fal.App, max_multiplexing=4):
    ...
```

This is especially effective for workloads with I/O waits (network calls, file downloads) where the GPU would otherwise sit idle between operations.

### Concurrency buffer

`concurrency_buffer` pre-warms runners before existing ones reach capacity. This reduces latency spikes but keeps more runners alive.

```python theme={null}
class MyApp(fal.App, max_multiplexing=4, concurrency_buffer=2):
    ...
```

Use conservatively — each buffered runner is billed while alive.

## Reduce Setup Time

Since `setup()` time is billed, faster initialization directly reduces cost per cold start. This matters most for applications with frequent cold starts (low `keep_alive`, bursty traffic).

> [card] [FlashPack](flashpack.md)
>
> High-throughput tensor loading for faster model initialization.

> [card] [Optimizing Cold Starts](optimize-cold-starts.md)
>
> Strategies for reducing container startup and setup time.

> [card] [Compiled Caches](optimize-startup-with-compiled-caches.md)
>
> Cache compiled kernels to skip recompilation on startup.

> [card] [Persistent Storage](../../development/use-persistent-storage.md)
>
> Use /data to cache downloads across runner restarts.

## Right-Size Your Machine Type

Don't over-provision GPU resources. A model that runs fine on an A6000 doesn't need an H100.

* Compare inference latency across machine types to find the smallest GPU that meets your requirements
* Consider that a cheaper machine running slightly longer may cost less than a faster, more expensive one
* See [Machine Types](../../deployment/machine-types.md) for available options

## Use Scaling Parameters Wisely

### `scaling_delay`

Prevents spinning up new runners for short traffic spikes. The platform waits this duration before provisioning additional runners.

```python theme={null}
class MyApp(fal.App, scaling_delay=15):  # Wait 15s before scaling up
    ...
```

Useful for workloads with brief bursts that don't justify new runners.

### `max_concurrency`

Caps the total number of concurrent runners. This sets a hard ceiling on your spend but may increase queue times during traffic spikes.

```python theme={null}
class MyApp(fal.App, max_concurrency=5):  # At most 5 runners
    ...
```

## Monitor and Iterate

Use the platform's observability tools to identify cost optimization opportunities:

* **[App Analytics](../observability/app-analytics.md)**: Identify apps with low utilization (high idle time relative to processing time)
* **[Error Analytics](../observability/error-analytics.md)**: Find apps with high error rates that waste compute on failed requests
* **[Exporting Metrics](../observability/exporting-metrics.md)**: Set up alerts for unusual spending patterns

> [!TIP]
>
> Start with the defaults, then monitor your App Analytics for a few days. Look for runners with high idle-to-processing ratios — those are your best candidates for `keep_alive` and `min_concurrency` adjustments.
