> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../documentation/quickstart.md) and the [Model APIs overview](../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Inference API

> Call fal models over HTTP with the queue API or the synchronous API, without an SDK.

The Inference API is the HTTP interface that the fal SDKs use. Use it from any language or HTTP client. This page gives the rules that apply to all operations. The operation pages give the request and response schemas. For model pricing, usage, and request history, see the [Platform API](platform-apis.md).

## Choose an API

| API | Host | Use it when |
| - | - | - |
| [Queue API](inference-api/queue/submit.md) | `https://queue.fal.run` | The request can take more than a few seconds, or you need retries, status, cancellation, or webhooks. |
| [Synchronous API](inference-api/sync/run.md) | `https://fal.run` | The request is short and you want the result in the same HTTP connection. |

Use the queue for production traffic. The queue keeps the request until a runner is available, and it retries the request after a temporary runner error. The synchronous API does not retry, and it returns an error when it cannot get a runner.

## Authentication

Send your API key in the `Authorization` header with the `Key` prefix:

```bash theme={null}
curl -X POST https://queue.fal.run/blackforestlabs/flux-3-max/text-to-video \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a red fox in the snow"}'
```

The key must have the permission for the operation:

| Operation | Permission |
| - | - |
| Submit a queue request, or run a model | `models:requests:submit` |
| Get the status or result of a request | `models:requests:read` |
| Cancel a request | `models:requests:cancel` |

The `API` preset has all three permissions. See [Key-based authentication](../documentation/model-apis/authentication/key-based.md).

## Model IDs and paths

A model ID has an owner, an app, and an optional endpoint path. For the model ID `blackforestlabs/flux-3-max/text-to-video`, the owner is `blackforestlabs`, the app is `flux-3-max`, and the path is `text-to-video`. Put the full model ID in the submit or run URL.

The status, result, and cancel URLs use only the owner and the app. Do not build these URLs yourself. Use the `status_url`, `response_url`, and `cancel_url` fields from the submit response.

## Queue request lifecycle

**1. Submit the request**

[Submit a request](inference-api/queue/submit.md). The response has the status `IN_QUEUE` and a `request_id`.

**2. Wait for completion**

[Get the status](inference-api/queue/status.md) until `status` is `COMPLETED`. The API returns `202` while the request is in the queue or in progress, and `200` when it is complete. To receive updates without polling, [stream the status](inference-api/queue/status-stream.md) or send a webhook URL with the submit request.

**3. Get the result**

[Get the result](inference-api/queue/result.md). If the request failed, this operation returns the error.

A failed or cancelled request also has the status `COMPLETED`. Get the result to find out if the request succeeded.

## Retries

The queue retries a request when the runner fails with a temporary error. Temporary errors are connection errors, `503`, and `504`. The queue makes up to 10 attempts. When your account has the maximum number of concurrent requests, the queue waits for capacity and does not count an attempt. Send `X-Fal-No-Retry: 1` to turn off retries for one request.

The synchronous API does not retry. When an error is temporary, the response has the header `X-Fal-Needs-Retry: 1`. Your client can retry these errors.

See [Reliability](../documentation/model-apis/inference/reliability.md).

## Timeouts

The `X-Fal-Request-Start-Timeout` header sets the maximum time in seconds for the request to start on a runner. The time includes queue time and retries. If the request does not start in this time, the API returns `504` with the header `X-Fal-Request-Timeout-Type: user` and the error type `startup_timeout`.

This header does not limit the run time on the runner. The model app sets the maximum run time. If the runner does not respond in that time, the API returns `504` with the error type `request_timeout`.

## Result retention

The queue keeps the result for 1 hour after the request completes. If the result is 10 KB or larger, the queue keeps it for 6 minutes. If payload storage is on, the result stays available for the storage period. Payload storage is on by default. Use the `X-Fal-Store-IO` header to change it for one request.

After the result expires, the result operation returns `404`. Get the result before it expires, or use a webhook.

## Errors

The error body has one of these shapes:

| Shape | Returned for |
| - | - |
| `{"detail": "...", "error_type": "..."}` | Errors from the request pipeline, for example timeouts and runner errors. The `X-Fal-Error-Type` header has the same value as `error_type`. See [Request Error Types](../documentation/model-apis/request-errors.md). |
| `{"detail": [{"loc": [...], "msg": "...", "type": "..."}]}` | Input errors from the model, with status `422`. See [Model Errors](../documentation/model-apis/errors.md). |
| `{"detail": "...", "type": "concurrent_requests_limit", "data": {...}}` | The synchronous API, with status `429`, when your account has the maximum number of concurrent requests. See [Concurrency limit errors](../documentation/model-apis/request-errors.md#concurrency-limit-errors). |
| `{"detail": "..."}` | Authentication, permission, and lookup errors. |
| `{"status": "NOT_FOUND"}` | Queue status and cancel operations for a request that does not exist. |

The queue API returns `404` for a request that belongs to a different account. It does not return `403`, so that the response does not show that the request exists.

## Response headers

| Header | Description |
| - | - |
| `x-fal-request-id` | The request ID. Include it when you contact support. |
| `X-Fal-Error-Type` | The error category of a failed request. |
| `X-Fal-Request-Timeout-Type` | `user` when the `X-Fal-Request-Start-Timeout` deadline passed. |
| `X-Fal-Needs-Retry` | `1` when a retry can succeed. `0` when a retry cannot succeed. |
| `X-Fal-Served-From` | The ID of the runner that processed the request. |
| `X-Fal-Billable-Units` | The billable units of the request, when the model reports them. |

For the request headers, see [Platform Headers](../documentation/model-apis/common-parameters.md).

## Backup hosts

Each host has a backup host. The backup host accepts the same API key, path, query parameters, and body.

| Host | Backup host |
| - | - |
| `queue.fal.run` | `queue.falrun.com` |
| `fal.run` | `falrun.com` |

When you use a backup host, replace the host in the `status_url`, `response_url`, and `cancel_url` values. See [Reliability](../documentation/model-apis/inference/reliability.md#backup-domains).

## Operations

> [card] [Submit request](inference-api/queue/submit.md)
>
> Add a model request to the queue.

> [card] [Get request status](inference-api/queue/status.md)
>
> Get the state of a queue request.

> [card] [Stream request status](inference-api/queue/status-stream.md)
>
> Receive status updates as server-sent events.

> [card] [Get request result](inference-api/queue/result.md)
>
> Get the output of a completed request.

> [card] [Cancel request](inference-api/queue/cancel.md)
>
> Remove a request from the queue or stop it.

> [card] [Run model](inference-api/sync/run.md)
>
> Run a model and wait for the result.
