> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../quickstart.md) and the [Model APIs overview](../overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Reliability

> How fal ensures high reliability for your API requests through queueing, automatic retries, and model fallbacks.

fal is designed for production workloads and includes several built-in mechanisms to ensure your requests succeed.

The platform applies three layers of protection automatically: a [queue](../inference.md) that absorbs traffic spikes, retries that recover from transient failures, and model fallbacks that reroute around unhealthy endpoints. All three are enabled by default and require no configuration.

## Queue-Based Processing

The [queue system](https://fal.ai/docs/model-apis/model-endpoints/queue.md) handles traffic surges gracefully and provides request tracking. When you submit a request, it enters a managed queue that ensures reliable processing even during peak demand.

## Automatic Retries

When using the [queue](https://fal.ai/docs/model-apis/model-endpoints/queue.md), fal automatically retries requests that fail due to:

* **Server errors (503)**: The model endpoint was temporarily unavailable
* **Timeouts (504)**: The request took too long due to transient issues
* **Connection errors**: Network issues between fal infrastructure
* **Concurrency limit (429)**: When you reach your account's [concurrent request limit](../concurrency-limits.md) or a per-endpoint limit, the request waits in the queue until capacity is available. This wait does not count as a retry.

Requests are retried up to 10 times with intelligent backoff.

> [!TIP]
>
> **Limit retry duration:** Use [start timeout](https://fal.ai/docs/model-apis/model-endpoints/queue.md#start-timeout) to cap the total time a request can spend waiting (including retries). Once the timeout is reached, no further retries occur.

> [!TIP]
>
> **No charge for server errors**: Failed requests that return 5xx status codes are not billed.

> [!NOTE]
>
> Automatic retries only apply to queue-based requests. Direct synchronous requests return errors immediately without retry.

For per-request control over retries and timeouts, see the Queue page -- including [disabling retries](https://fal.ai/docs/model-apis/model-endpoints/queue.md#disabling-retries) with the `X-Fal-No-Retry` header, setting a [start timeout](https://fal.ai/docs/model-apis/model-endpoints/queue.md#start-timeout) with `X-Fal-Request-Start-Timeout`, and using [client timeout](https://fal.ai/docs/model-apis/model-endpoints/queue.md#client-timeout) to set a client-side deadline.

| Timeout | Enforced by | Effect on retries |
| - | - | - |
| `start_timeout` / `X-Fal-Request-Start-Timeout` | Server | Stops retries when total elapsed time is exceeded |
| `client_timeout` (Python SDK, `subscribe` only) | Client | Client stops polling; server and retries continue |

## Backup Domains

Backup domains provide an alternative connection to the same inference API when a primary domain is unreachable.

| Primary domain | Backup domain | Requests |
| - | - | - |
| `fal.run` | `falrun.com` | Direct inference and HTTP streaming (SSE) |
| `queue.fal.run` | `queue.falrun.com` | Queue submission, status, results, and cancellation |

Use the same API key, request path, query parameters, and body with the backup domain.
If your network restricts outbound connections, allow HTTPS access to both primary and backup domains.

### Python Client

The Python `fal-client` tries the corresponding backup once if the primary connection fails or times out.
This applies to `run()`, `submit()`, `subscribe()`, queue operations, and `stream()`, including their async counterparts.
No configuration is required.

### Raw HTTP Requests

For direct HTTP calls, replace the hostname when connection establishment fails. For example:

```bash theme={null}
curl -X POST "https://falrun.com/fal-ai/flux/schnell" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a sunset"}'
```

If the primary remains unreachable, replace the hostname in returned queue status, result, and cancellation URLs.

### Coverage

Python client fallback covers HTTP inference requests only. It does not cover WebSocket or realtime connections, or the separate `fal.apps` client.
Token requests to `rest.fal.ai` and uploads to `v3.fal.media` do not have backup domains.

## Model Fallbacks

For supported models, fal can send a request to an equivalent endpoint when the primary endpoint is temporarily unavailable. The model app makes this decision for each request. It uses the fallback endpoint when the primary endpoint fails with a service error, a network error, or a timeout. This mechanism improves overall reliability and reduces the likelihood of failed requests.

Fallbacks are enabled by default for all accounts. If you need to disable fallbacks for your account, please let your account team know. If you want to disable it per request, you can pass the `x-app-fal-disable-fallback` header. For any questions, contact our sales team.

> [!TIP]
>
> To select your own fallback models and the errors that start them, use a [fallback chain](fallback-chains.md).
