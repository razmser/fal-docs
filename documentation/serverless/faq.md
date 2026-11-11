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
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Serverless FAQ

> Answers about fal Serverless: per-second billing, idle runners, GPU types, cold starts, autoscaling, timeouts, Docker images, rollbacks, and concurrency.

Common questions about deploying and running your own apps on fal Serverless. For questions about calling pre-trained models, see the [Model APIs FAQ](../model-apis/faq.md).

> [accordion] How easy is it to migrate from another platform?
>
> If you already have a working Docker container or a Python inference server, migrating to fal is straightforward. You can bring your existing Dockerfile directly with [custom container images](../development/use-custom-container-image.md), or wrap your model in a `fal.App` class with `setup()` and endpoint methods. fal has step-by-step migration guides for [Replicate](../development/migrate-from-replicate.md), [Modal](../development/migrate-from-modal.md), [RunPod](../development/migrate-from-runpod.md), and [generic Docker servers](../development/migrate-external-docker-server.md).

> [accordion] Can I export metrics to my own monitoring stack?
>
> Yes. fal exposes a Prometheus-compatible [metrics endpoint](observability/exporting-metrics.md) that you can scrape with Grafana, Datadog, or any tool that reads Prometheus format. Metrics include request counts, queue depth, runner states, and latency percentiles, broken down by app and endpoint. You can also forward logs to external services with [log drains](observability/log-drains.md).

> [accordion] How do I improve runner utilization?
>
> The two most impactful settings are `keep_alive` and `scaling_delay`. Lowering `keep_alive` reduces idle time by shutting down runners sooner after they finish processing, which avoids paying for runners that are waiting for requests that never come. Increasing `scaling_delay` prevents fal from spinning up new runners too quickly during short traffic bursts, giving existing runners a chance to absorb the load first. Together these help you avoid over-provisioning. For further tuning, `max_multiplexing` lets a single runner handle multiple requests concurrently, and `concurrency_buffer` pre-warms runners ahead of demand. See [Optimizing Costs](optimizations/optimizing-costs.md) for the full set of strategies.

> [accordion] How do I control retry behavior?
>
> fal automatically [retries](reliability/retries.md) failed queue requests up to 10 times for server errors, timeouts, and connection failures. You can disable retries for specific conditions by setting `skip_retry_conditions` on your app (e.g., `skip_retry_conditions=["timeout"]` if your model legitimately takes a long time). You can also disable retries for individual responses by setting the `X-Fal-Needs-Retry: 0` header from your endpoint, or per-request by the caller with the `X-Fal-No-Retry: 1` header.

> [accordion] How am I billed for Serverless?
>
> You are billed per-second for the total time your runners are alive, at the rate for your chosen [machine type](../deployment/machine-types.md). This includes `setup()`, idle time (including `keep_alive`), active request processing, draining, and teardown. You are not billed for pending time or container image pulls. See [Serverless Pricing](pricing.md) for the full breakdown by runner state.

> [accordion] Am I billed for idle runners?
>
> Yes. Runners in the `IDLE` state are billed at the same per-second rate as running requests. The `keep_alive` setting controls how long a runner stays alive after finishing its last request. Set `keep_alive = 0` to shut down immediately after each request, or use `min_concurrency` to keep warm runners available. See [Optimizing Costs](optimizations/optimizing-costs.md) for strategies to balance cost and cold starts.

> [accordion] What GPU types are available?
>
> fal offers CPU instances (XS through L) and GPU instances including RTX 4090, RTX 5090, A100, L40, H100, H200, and B200. Multi-GPU configurations are available for distributed workloads. See [Machine Types](../deployment/machine-types.md) for full specs and guidance on choosing the right GPU.

> [accordion] How do I reduce cold start times?
>
> Cold starts happen when no warm runner is available and a new one must be provisioned. You can reduce them by setting `min_concurrency` to keep runners warm, using [FlashPack](optimizations/flashpack.md) to load model weights onto the GPU faster, storing model weights on [persistent storage](../development/use-persistent-storage.md) (`/data`) to avoid re-downloads, and optimizing your container image. See [Optimizing Cold Starts](optimizations/optimize-cold-starts.md) for the full guide.

> [accordion] What is the /data volume?
>
> Every runner has access to a shared [persistent storage](../development/use-persistent-storage.md) volume mounted at `/data`. Files written to `/data` persist across requests, runner restarts, and deployments. It is backed by a multi-layer cache (local NVMe, datacenter cache, global object store) and is the recommended place to store model weights, datasets, and configuration files.

> [accordion] Can I use my own Docker image?
>
> Yes. You can specify a Dockerfile or reference a pre-built image from any container registry, including private registries. See [Using a Custom Container Image](../development/use-custom-container-image.md) for setup instructions.

> [accordion] How does autoscaling work?
>
> fal automatically scales runners up and down based on incoming request volume. You control the behavior with `min_concurrency` (minimum warm runners), `max_concurrency` (maximum runners), `max_multiplexing` (requests per runner), and `keep_alive` (idle timeout). See [Scaling Your Application](../deployment/scale-your-application.md) for details.

> [accordion] What is the maximum request timeout?
>
> The default request timeout is 3600 seconds (1 hour). You can configure this per-app with the `request_timeout` setting in your app class. Callers can also set a client-side [`start_timeout`](../model-apis/inference/queue.md#start_timeout) to limit how long they wait before processing begins.

> [accordion] Can I run multiple models on one endpoint?
>
> Yes. You can load multiple models in `setup()` and route between them based on request input, or use [multi-app routing](../development/multi-app-routing.md) to serve different models from different endpoints within a single deployment. You can also use [runner hints](https://fal.ai/docs/documentation/serverless/optimizations/optimize-routing-behavior.md) to pin requests to runners with specific models loaded.

> [accordion] How do I rollback a bad deployment?
>
> Every deployment creates a new revision. You can roll back to any previous revision from the dashboard or CLI. See [Rollbacks](../deployment/rollbacks.md) for instructions.

> [accordion] Can I access logs from my running app?
>
> Yes. Logs from your app are available in the dashboard, via the CLI (`fal apps runners`), and through the [Platform APIs](../../api-reference/platform-apis/for-serverless.md). You can also configure [log drains](observability/log-drains.md) to forward logs to external services. See [Logging](../development/logging.md) for how to emit structured logs from your app.

> [accordion] Do concurrency limits apply to my own endpoints?
>
> No. When you call your own Serverless endpoints, [concurrency limits](../model-apis/concurrency-limits.md) are not enforced. Your throughput is limited by your `max_concurrency` scaling configuration, not by the platform-level account concurrency limit.
