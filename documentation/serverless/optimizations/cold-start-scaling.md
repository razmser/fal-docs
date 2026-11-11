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

# Adjust Scaling Parameters

> Reduce cold starts by keeping warm runners available.

The most effective way to reduce cold starts is maintaining warm runners using scaling parameters. These control how many runners stay alive and how quickly new ones spin up.

## `keep_alive`

**Default:** 60 seconds

Keep idle runners alive before shutdown. This applies after a runner finishes a request and when a newly started runner gets no request.

```python theme={null}
class MyApp(fal.App):
    keep_alive = 300  # Keep alive for 5 minutes
```

**Benefits:** Runners stay warm between requests, reduces cold starts for sporadic traffic

**Trade-offs:** Longer keep\_alive = higher costs; shorter = more cold starts

## `min_concurrency`

**Default:** 0

Maintain minimum runners alive at all times, regardless of traffic.

```python theme={null}
class MyApp(fal.App):
    min_concurrency = 2  # Always keep 2 runners warm
```

**Benefits:** Guarantees warm runners always available, eliminates cold starts for baseline capacity

**Trade-offs:** Costs money even with zero traffic

## `concurrency_buffer`

**Default:** 0

Maintain extra runners beyond current demand.

```python theme={null}
class MyApp(fal.App):
    concurrency_buffer = 2  # Keep 2 extra runners ready
```

**Benefits:** Cushion for sudden traffic increases, reduces cold starts during bursts

**Trade-offs:** Higher cost during all traffic levels

> [!NOTE]
>
> Takes precedence over `min_concurrency` when higher.

## `concurrency_buffer_perc`

**Default:** 0

Set buffer as a percentage of current request volume.

```python theme={null}
class MyApp(fal.App):
    concurrency_buffer_perc = 20  # 20% buffer
```

**Benefits:** Scales buffer with traffic automatically

**Trade-offs:** No buffer during zero traffic, expensive during high traffic

> [!NOTE]
>
> Actual buffer is the maximum of `concurrency_buffer` and `concurrency_buffer_perc / 100 * request volume`.

## `max_multiplexing`

**Default:** 1

Number of concurrent requests each runner handles simultaneously.

```python theme={null}
class MyApp(fal.App):
    max_multiplexing = 4  # Each runner handles up to 4 requests
```

**Benefits:** Fewer runners needed, fewer cold starts, better resource utilization

**Trade-offs:** Must use async handlers, each request gets fewer resources, not suitable for all workloads

## `scaling_delay`

**Default:** 0 seconds

Wait time before scaling up when a request is queued.

```python theme={null}
class MyApp(fal.App):
    scaling_delay = 30  # Wait 30 seconds before scaling
```

**Benefits:** Prevents premature scaling for brief spikes, reduces unnecessary cold starts

**Trade-offs:** Requests wait longer during genuine traffic increases

## `startup_timeout`

**Default:** Varies

Maximum time allowed for `setup()` to complete.

```python theme={null}
class MyApp(fal.App):
    startup_timeout = 600  # 10 minutes for setup
```

**Benefits:** Prevents runners from being killed during long setups, accommodates large model loading

**Trade-offs:** Doesn't reduce cold starts (only prevents failed startups), long timeouts can mask real issues

## Persistence Across Deploys

Scaling parameters set via CLI or dashboard (`keep_alive`, `min_concurrency`, `concurrency_buffer`, etc.) **persist across deployments** by default. You don't lose your tuning when you deploy a code change.

To reset all parameters back to code values, deploy with `--reset-scale`:

```bash theme={null}
fal deploy --reset-scale
```

> [card] [Deploy Behavior & Priority](../../deployment/scale-your-application.md#deploy-behavior-and---reset-scale)
>
> Full explanation of how code, CLI, and dashboard settings interact

## Cost Considerations

More warm runners = lower latency but higher cost. Balance based on your needs:

* **Latency-critical apps**: Accept higher cost for warm runners (`min_concurrency`, `keep_alive`)
* **Cost-sensitive apps**: Optimize cold start duration instead (container images, caching)
* **Variable traffic**: Use buffers and scaling delays

> [card] [Full Scaling Reference](../../deployment/scale-your-application.md)
>
> Complete guide to scaling configuration including CLI and dashboard methods
