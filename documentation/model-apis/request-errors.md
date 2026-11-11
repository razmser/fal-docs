> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Request Error Types

> Infrastructure-level error types for timeouts, runner failures, and connection errors.

When a request fails due to infrastructure-level issues rather than model validation, the platform returns a structured error response with a machine-readable `error_type` field. These errors cover timeouts, runner crashes, scheduling failures, and connection problems that occur before or during request processing.

Request errors use a different response format than [model validation errors](errors.md). Model errors return a `detail` array of typed error objects. Request errors return a flat object with `detail` as a human-readable string and `error_type` as a machine-readable category. The same value is also available in the `X-Fal-Error-Type` response header for programmatic access without parsing the body.

## Response Structure

| Property | Description |
| :- | :- |
| `detail` | A human-readable description of the error. |
| `error_type` | A machine-readable string identifying the error category (see table below). |

```json theme={null}
{
  "detail": "Request timed out",
  "error_type": "request_timeout"
}
```

The `X-Fal-Error-Type` response header contains the same value as `error_type`.

## Error Type Reference

| Error Type | Description | Typical Status Code |
| :- | :- | :- |
| `request_timeout` | The request exceeded the allowed processing time. | 504 |
| `startup_timeout` | The runner did not start within the allowed time. | 504 |
| `runner_scheduling_failure` | No runner could be allocated to handle the request. | 503 |
| `runner_connection_timeout` | The connection to the runner timed out. | 503 |
| `runner_disconnected` | The runner disconnected unexpectedly during processing. | 503 |
| `runner_connection_refused` | The runner refused the connection. | 503 |
| `runner_connection_error` | A general connection error occurred with the runner. | 503 |
| `runner_incomplete_response` | The runner sent an incomplete response payload. | 502 |
| `runner_server_error` | The runner encountered an internal server error. | 500 |
| `client_disconnected` | The client closed the connection before the response was sent. | 499 |
| `client_cancelled` | The request was cancelled by the client. | 499 |
| `bad_request` | The request was malformed (e.g., invalid timeout header). | 400 |
| `internal_error` | An unexpected internal error occurred. | 500 |

## Concurrency Limit Errors

The synchronous API returns `429` when you reach your account's [concurrent request limit](concurrency-limits.md) or a per-endpoint limit. This response does not have an `error_type` field. It has a `type` field and a `data` object:

```json theme={null}
{
  "detail": "Reached concurrent requests limit of 10",
  "type": "concurrent_requests_limit",
  "data": {"limit": 10, "notify": true}
}
```

The `data.limit` field is the limit that applies. The `data.notify` field is `true` for the account limit and `false` for a per-endpoint limit. The response also has the headers `X-Fal-Needs-Retry: 1` and `X-Fal-Needs-Retry-Type: concurrent_requests_limit`. Retry the request after one of your requests completes.

The queue API does not return this error. The queue keeps the request until capacity is available.

## Handling Request Errors

**For retry logic:** Use `error_type` to decide whether to retry. Runner and timeout errors (e.g., `runner_connection_timeout`, `startup_timeout`) are typically transient and worth retrying. Client errors (`client_disconnected`, `bad_request`) should not be retried. See [Retries and Error Handling](../serverless/reliability/retries.md) for how the platform handles retries automatically.

**For monitoring:** The `error_type` is also available in [queue status responses](inference/queue.md#request-lifecycle) for failed requests, making it useful for tracking failure patterns in your [analytics dashboard](../serverless/observability/app-analytics.md).

> [card] [Model Errors](errors.md)
>
> Validation errors from model inputs (images, video, audio)

> [card] [Retries](../serverless/reliability/retries.md)
>
> How fal automatically retries failed requests
