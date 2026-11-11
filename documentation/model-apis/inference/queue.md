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

# Queue API: Submit, Check Status, Get Results

> Submit requests to queue.fal.run, then poll or stream the status (IN_QUEUE, IN_PROGRESS, COMPLETED), fetch the result, cancel, or receive it by webhook.

Asynchronous inference is the recommended way to call models on fal. You submit a request to a persistent [queue](reliability.md), then retrieve results later by polling for status or receiving them via [webhook](webhooks.md). If you prefer a simpler blocking call that handles polling for you automatically, use [subscribe](synchronous.md) instead.

The async approach gives you full control over the request lifecycle. You can submit many requests in parallel and process them as they complete, get real-time visibility into queue position and [runner](../../deployment/runners.md) logs, and rely on [automatic retries](../../serverless/reliability/retries.md) when failures occur. All of this works identically whether you are calling a pre-trained model from the gallery or your own app deployed on [Serverless](../../serverless.md). This page covers the full queue API: submitting requests, checking status, streaming updates, retrieving results, cancelling, and configuring webhooks. For the HTTP request and response schemas of each queue operation, see the [Queue API reference](../../../api-reference/inference-api/queue/submit.md).

***

## How the Queue Works

When you submit a request to `queue.fal.run`, it enters a persistent, durable queue. The request moves through three states before completion:

### Request Lifecycle

| Status | SDK Type (Python / JS) | What is happening |
| - | - | - |
| `IN_QUEUE` | `Queued(position)` / `"IN_QUEUE"` | Request is received and stored. Waiting for an available runner. |
| `IN_PROGRESS` | `InProgress(logs)` / `"IN_PROGRESS"` | fal's dispatcher has routed the request to a runner. |
| `COMPLETED` | `Completed(logs, metrics)` / `"COMPLETED"` | Result is stored and available for retrieval, or sent to your webhook. |

See the [Python SDK reference](../../../api-reference/client-libraries/python/fal_client.md#status) or [JavaScript SDK reference](../../../api-reference/client-libraries/javascript/types.common.md) for the full type definitions.

### Key Guarantees

Requests in the queue are never dropped. If no runners are available, your request waits while fal [scales up new runners](../../deployment/scale-your-application.md) automatically. There is no queue size limit. If a runner fails during processing (503, 504, or a connection error), the request is automatically re-queued and [retried](reliability.md) up to 10 times. As demand grows, runners scale up to match. When demand drops, they scale back down, so you only pay for compute you use.

***

## Submit a Request

Use `submit` to send a request to the queue and return immediately. In Python, `submit()` returns a `SyncRequestHandle` object with methods for status, result, and cancel (`submit_async()` returns an `AsyncRequestHandle`). In JavaScript, `submit()` returns an object with the `request_id`, and you use separate `fal.queue.*` functions. In the REST API, the response includes URLs for each operation.

```python Python theme={null}
import fal_client

handler = fal_client.submit("fal-ai/flux/schnell", arguments={
    "prompt": "a sunset over mountains"
})

print(handler.request_id)
```

```python Python (async) theme={null}
import asyncio
import fal_client

async def main():
    handler = await fal_client.submit_async("fal-ai/flux/schnell", arguments={
        "prompt": "a sunset over mountains"
    })
    print(handler.request_id)

asyncio.run(main())
```

```javascript JavaScript theme={null}
import { fal } from "@fal-ai/client";

const { request_id } = await fal.queue.submit("fal-ai/flux/schnell", {
  input: { prompt: "a sunset over mountains" },
});

console.log(request_id);
```

```bash cURL theme={null}
curl -X POST https://queue.fal.run/fal-ai/flux/schnell \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a sunset over mountains"}'
```

The submit response includes the `request_id` and convenience URLs for tracking the request:

```json theme={null}
{
  "request_id": "764cabcf-b745-4b3e-ae38-1200304cf45b",
  "response_url": "https://queue.fal.run/fal-ai/flux/requests/764cabcf...",
  "status_url": "https://queue.fal.run/fal-ai/flux/requests/764cabcf.../status",
  "cancel_url": "https://queue.fal.run/fal-ai/flux/requests/764cabcf.../cancel",
  "queue_position": 0
}
```

These URLs use only the owner and the app (`fal-ai/flux`), not the full model ID. A URL that contains the endpoint path, for example `fal-ai/flux/schnell/requests/...`, returns `405`. Use the URLs from the submit response. If you build a URL yourself, use only the owner and the app.

Store the `request_id` if you need to check status or retrieve results later, even from a different process.

***

## Check Status

Poll for the current state of the request. Status updates do not require request logs.

The app owner can request logs with `with_logs=True` (Python), `logs: true` (JavaScript), or `?logs=1` (REST). Non-owner callers receive empty log arrays, even for their own requests. For your own deployed apps, see [request-log restrictions](../../development/logging.md#request-logs).

```python Python theme={null}
status = handler.status(with_logs=True)
if isinstance(status, fal_client.Queued):
    print(f"Position: {status.position}")
elif isinstance(status, fal_client.InProgress):
    print("Processing...")
```

```python Python (async) theme={null}
status = await handler.status(with_logs=True)
if isinstance(status, fal_client.Queued):
    print(f"Position: {status.position}")
elif isinstance(status, fal_client.InProgress):
    print("Processing...")
```

```javascript JavaScript theme={null}
const status = await fal.queue.status("fal-ai/flux/schnell", {
  requestId: request_id,
  logs: true,
});
if (status.status === "IN_QUEUE") {
  console.log(`Position: ${status.queue_position}`);
} else if (status.status === "IN_PROGRESS") {
  console.log("Processing...");
}
```

```bash cURL theme={null}
curl "https://queue.fal.run/fal-ai/flux/requests/{request_id}/status?logs=1" \
  -H "Authorization: Key $FAL_KEY"
```

The response shape depends on the current status:

**IN\_QUEUE** -- waiting for an available runner:

```json theme={null}
{
  "status": "IN_QUEUE",
  "request_id": "764cabcf-...",
  "queue_position": 2,
  "response_url": "https://queue.fal.run/fal-ai/flux/requests/764cabcf-..."
}
```

**IN\_PROGRESS** -- a runner is processing the request:

```json theme={null}
{
  "status": "IN_PROGRESS",
  "request_id": "764cabcf-...",
  "response_url": "https://queue.fal.run/fal-ai/flux/requests/764cabcf-...",
  "logs": []
}
```

**COMPLETED** -- the result is ready to retrieve:

```json theme={null}
{
  "status": "COMPLETED",
  "request_id": "764cabcf-...",
  "response_url": "https://queue.fal.run/fal-ai/flux/requests/764cabcf-...",
  "logs": [],
  "metrics": {"inference_time": 3.42}
}
```

| Field | Description |
| - | - |
| `queue_position` | Number of requests ahead of yours (only when `IN_QUEUE`) |
| `logs` | Request logs, when requested and available to the app owner. Empty for non-owner callers. |
| `metrics.inference_time` | Seconds the runner spent processing (only when `COMPLETED`) |
| `error` | A human-readable error message, present only if the request failed (only when `COMPLETED`) |
| `error_type` | A machine-readable error type string, present only if the request failed. See [Request Error Types](../request-errors.md) for the full list of values. |

***

## Stream Status Updates

Instead of polling manually, you can stream status updates continuously until the request completes. In Python, `iter_events()` polls and yields each status. In JavaScript, `streamStatus()` opens an SSE connection.

Log access is the [same as when polling](#check-status). Non-owner callers can track request status but receive no request logs.

```python Python theme={null}
for event in handler.iter_events():
    if isinstance(event, fal_client.Queued):
        print(f"Position: {event.position}")
    elif isinstance(event, fal_client.InProgress):
        print("Processing...")

result = handler.get()
```

```python Python (async) theme={null}
async for event in handler.iter_events():
    if isinstance(event, fal_client.Queued):
        print(f"Position: {event.position}")
    elif isinstance(event, fal_client.InProgress):
        print("Processing...")

result = await handler.get()
```

```javascript JavaScript theme={null}
const stream = await fal.queue.streamStatus("fal-ai/flux/schnell", {
  requestId: request_id,
});

for await (const status of stream) {
  if (status.status === "IN_QUEUE") {
    console.log(`Position: ${status.queue_position}`);
  } else if (status.status === "IN_PROGRESS") {
    console.log("Processing...");
  } else if (status.status === "COMPLETED") {
    console.log(`Done in ${status.metrics?.inference_time}s`);
  }
}
```

```bash cURL theme={null}
curl "https://queue.fal.run/fal-ai/flux/requests/{request_id}/status/stream" \
  -H "Authorization: Key $FAL_KEY"
```

The REST streaming endpoint returns `text/event-stream` with SSE events. Each event is a JSON status object in the same format as the polling endpoint. The connection stays open until the status reaches `COMPLETED`.

***

## Get the Result

Retrieve the final output once the request is complete. In Python, `get()` polls internally until `Completed` then fetches the response. In JavaScript, call `fal.queue.result()` after the status is `COMPLETED`.

```python Python theme={null}
result = handler.get()
print(result["images"][0]["url"])
```

```python Python (async) theme={null}
result = await handler.get()
print(result["images"][0]["url"])
```

```javascript JavaScript theme={null}
const result = await fal.queue.result("fal-ai/flux/schnell", {
  requestId: request_id,
});
console.log(result.data.images[0].url);
```

```bash cURL theme={null}
curl https://queue.fal.run/fal-ai/flux/requests/{request_id} \
  -H "Authorization: Key $FAL_KEY"
```

The result structure is **model-specific**. For example, an image model returns:

```json theme={null}
{
  "images": [
    {
      "url": "https://v3.fal.media/files/rabbit/abc123.png",
      "width": 1024,
      "height": 1024,
      "content_type": "image/png"
    }
  ],
  "prompt": "a sunset over mountains",
  "seed": 42,
  "has_nsfw_concepts": [false]
}
```

Video generation models return a `video` object, and audio/speech models return an `audio_url` or `audio` object. Check the model's API page (e.g., [FLUX.1 schnell API](https://fal.ai/models/fal-ai/flux/schnell/api)) for the exact output schema.

> [!NOTE]
>
> All media URLs in responses (`https://v3.fal.media/...`) are publicly accessible and subject to your [media expiration settings](../media-expiration.md). Download files you need to keep before they expire.

***

## Cancel a Request

Cancel a request. What happens depends on the request's state:

* **Still in the queue (IN\_QUEUE):** The request is removed immediately and is never processed.
* **Already being processed (IN\_PROGRESS):** fal sends a cancellation signal to the runner. The request may still complete if the app does not handle cancellation. Whether the running code actually stops depends on whether the app has implemented a cancel endpoint.

If you are deploying your own serverless app and want in-progress requests to stop cleanly when cancelled, see [Handle Cancellations](../../development/handle-cancellations.md) for how to implement a cancel endpoint.

```python Python theme={null}
handler.cancel()
```

```python Python (async) theme={null}
await handler.cancel()
```

```javascript JavaScript theme={null}
await fal.queue.cancel("fal-ai/flux/schnell", {
  requestId: request_id,
});
```

```bash cURL theme={null}
curl -X PUT https://queue.fal.run/fal-ai/flux/requests/{request_id}/cancel \
  -H "Authorization: Key $FAL_KEY"
```

The response includes both an HTTP status code and a JSON body:

| HTTP Status | JSON Body | Meaning |
| - | - | - |
| `200 OK` | `{"status": "OK"}` | The request was in the queue. fal removed it, and it does not run. |
| `202 Accepted` | `{"status": "CANCELLATION_REQUESTED"}` | The request is in progress. fal sent a cancellation signal to the runner. The request can still complete. |
| `400 Bad Request` | `{"status": "ALREADY_COMPLETED"}` | The request already finished before the cancel arrived. |
| `404 Not Found` | `{"status": "NOT_FOUND"}` | No request exists with that ID, or the request belongs to a different account. |

When using the SDK, `handler.cancel()` succeeds silently on `200` or `202` and raises an exception on `400` or `404`.

***

## Webhooks

Instead of polling, configure fal to POST results directly to your server when a request completes.

```python Python theme={null}
import fal_client

handler = fal_client.submit("fal-ai/flux/schnell",
    arguments={"prompt": "a sunset over mountains"},
    webhook_url="https://your-server.com/webhook",
)

print(f"Request submitted: {handler.request_id}")
```

```python Python (async) theme={null}
import fal_client

handler = await fal_client.submit_async("fal-ai/flux/schnell",
    arguments={"prompt": "a sunset over mountains"},
    webhook_url="https://your-server.com/webhook",
)

print(f"Request submitted: {handler.request_id}")
```

```javascript JavaScript theme={null}
const { request_id } = await fal.queue.submit("fal-ai/flux/schnell", {
  input: { prompt: "a sunset over mountains" },
  webhookUrl: "https://your-server.com/webhook",
});
```

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/fal-ai/flux/schnell?fal_webhook=https://your-server.com/webhook" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a sunset over mountains"}'
```

When complete, fal sends a POST to your webhook URL:

```json theme={null}
{
  "request_id": "abc123",
  "gateway_request_id": "abc123",
  "status": "OK",
  "payload": {
    "images": [
      {"url": "https://fal.media/files/...", "width": 1024, "height": 1024}
    ]
  }
}
```

The webhook `status` is `"OK"` for successful responses (HTTP 200) or `"ERROR"` for failures -- this is different from the queue status values (`IN_QUEUE`, `IN_PROGRESS`, `COMPLETED`). Return 200 quickly to acknowledge the webhook. fal may retry failed deliveries, so use `request_id` for idempotency. See [Webhooks](webhooks.md) for full details on payload format, retries, and signature verification.

***

## `submit()` Parameters

### `path`

Endpoint path appended to the model ID. Most models expose a single root endpoint, so you can leave this empty. Use it when a model or your own app defines additional endpoints at sub-paths.

```python Python theme={null}
handler = fal_client.submit("fal-ai/nano-banana-2", arguments={...}, path="/custom-endpoint")
```

```javascript JavaScript theme={null}
const { request_id } = await fal.queue.submit("fal-ai/nano-banana-2/custom-endpoint", {
  input: {...},
});
```

In JavaScript, append the path directly to the endpoint string.

### `start_timeout`

Server-side deadline in seconds, sent as the `X-Fal-Request-Start-Timeout` header. This sets an absolute wall-clock deadline, computed once when the request is submitted. The server checks this deadline before processing begins. If the deadline has passed, the server returns a 504 without processing the request.

If a runner picks up the request but **fails** (e.g., returns 503 or crashes), the request goes back to the queue for a retry. Because the deadline is a fixed timestamp, all elapsed time -- including time spent on the failed attempt -- counts against the same original deadline. If the deadline is reached before any runner successfully starts processing, the server returns `504 Gateway Timeout` with the header `X-Fal-Request-Timeout-Type: user` and no further retries occur.

> [!NOTE]
>
> This timeout does not limit how long inference takes. Once a runner starts processing, the deadline is not enforced and the request runs to completion. This is different from `request_timeout`, which limits each individual processing attempt and actively kills the runner if exceeded. The maximum inference time is controlled by the app's `request_timeout` setting (default 3600s), which is configured by the app developer, not the caller. If you need a total client-side deadline that includes processing time, use [`client_timeout`](#client_timeout-subscribe-only) on `subscribe()`.

For a full comparison of timeout mechanisms, see [Timeouts and Retries](../../serverless/reliability/retries.md#timeouts-and-retries).

```python Python theme={null}
handler = fal_client.submit("fal-ai/nano-banana-2", arguments={...}, start_timeout=30)
```

```javascript JavaScript theme={null}
const { request_id } = await fal.queue.submit("fal-ai/nano-banana-2", {
  input: {...},
  startTimeout: 30,
});
```

### `hint`

Routing hint sent as the `X-Fal-Runner-Hint` header. When you pass a hint string, fal tries to route the request to the same runner that handled a previous request with the same hint. This is useful for session affinity -- for example, keeping requests pinned to a runner that already has a specific model or adapter loaded in memory. For serverless apps that serve multiple models, your app can implement `provide_hints()` on the server side to tell fal what each runner is specialized for. See [Optimize Routing Behavior](https://fal.ai/docs/documentation/serverless/optimizations/optimize-routing-behavior.md) for the full pattern.

```python Python theme={null}
handler = fal_client.submit("fal-ai/nano-banana-2", arguments={...}, hint="user-session-abc")
```

```javascript JavaScript theme={null}
const { request_id } = await fal.queue.submit("fal-ai/nano-banana-2", {
  input: {...},
  hint: "user-session-abc",
});
```

### `priority`

Queue priority for the request, sent as the `X-Fal-Queue-Priority` header. Accepts `"normal"` (default) or `"low"`.

Priority applies to the **per-user per-endpoint queue**. Therefore setting `priority="low"` on a shared model API (like `fal-ai/flux/dev`) deprioritizes your request relative to your other requests. Requests between users are fairly prioritized.

For example, you might submit user-facing requests at normal priority and background batch jobs at low priority to give interactive requests higher queue priority.

```python Python theme={null}
handler = fal_client.submit("your-username/your-app", arguments={...}, priority="low")
```

```javascript JavaScript theme={null}
const { request_id } = await fal.queue.submit("your-username/your-app", {
  input: {...},
  priority: "low",
});
```

### `webhook_url`

URL where fal sends a POST request with the result when processing completes. When set, you don't need to poll for status -- the result arrives at your server automatically. The webhook payload includes the `request_id`, `status`, and the full model output. See [Webhooks](webhooks.md) for payload format, retries, and signature verification.

```python Python theme={null}
handler = fal_client.submit(
    "fal-ai/nano-banana-2",
    arguments={"prompt": "a sunset"},
    webhook_url="https://your-server.com/api/fal/webhook",
)
```

```javascript JavaScript theme={null}
const { request_id } = await fal.queue.submit("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset" },
  webhookUrl: "https://your-server.com/api/fal/webhook",
});
```

### `headers`

Additional HTTP headers passed with the request. Use this to set [platform-level headers](../common-parameters.md) like `X-Fal-Store-IO` (disable payload storage), `X-Fal-No-Retry` (disable retries), or `X-Fal-Object-Lifecycle-Preference` (control media expiration).

```python Python theme={null}
handler = fal_client.submit("fal-ai/nano-banana-2", arguments={...}, headers={"X-Fal-No-Retry": "1"})
```

```javascript JavaScript theme={null}
const { request_id } = await fal.queue.submit("fal-ai/nano-banana-2", {
  input: {...},
  headers: { "X-Fal-No-Retry": "1" },
});
```

***

## `subscribe()` Parameters

`subscribe()` accepts all of the `submit()` parameters above (`path`, `start_timeout`, `hint`, `priority`, `headers`), plus the following. In JavaScript, `fal.subscribe()` supports `priority`, `headers`, and `webhookUrl` as named options; for `hint`, use [`fal.queue.submit()`](#hint) directly.

### `client_timeout` / `timeout`

Client-side deadline that limits the total time the client waits for the result, including queue wait and processing. When exceeded, the client stops polling and raises an error. The request may still be processing on the server. In Python the parameter is called `client_timeout` and accepts seconds. In JavaScript the parameter is called `timeout` and accepts milliseconds.

```python Python theme={null}
result = fal_client.subscribe(
    "fal-ai/flux/dev",
    arguments={"prompt": "a cat"},
    client_timeout=60,  # client gives up after 60 seconds
)
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/flux/dev", {
  input: { prompt: "a cat" },
  timeout: 60000,  // client gives up after 60000 milliseconds
});
```

**Interaction with `start_timeout` (Python):** If you set `client_timeout` without setting `start_timeout`, the SDK automatically sets `start_timeout = client_timeout` so the server also respects your deadline. If you explicitly set `start_timeout` to a value larger than `client_timeout`, the SDK emits a warning because the server-side timeout would never be reached before the client gives up.

| Timeout | Enforced by | Scope | Affects server | Use when |
| - | - | - | - | - |
| `start_timeout` / `X-Fal-Request-Start-Timeout` | Server | Absolute deadline across entire lifecycle (enforced before processing only) | Yes (returns 504, stops retries) | You want the server to cancel the request before it starts |
| `client_timeout` (Python) / `timeout` (JS) | Client | Total wall-clock time including processing | No (request may continue) | You want a local deadline without affecting the server |
| `request_timeout` | App developer | Per-attempt processing limit (resets on each retry) | Yes (kills runner, triggers retry) | Set by the app to cap individual inference time |

***

## Disabling Retries

By default, fal [automatically retries](../../serverless/reliability/retries.md) queue requests that fail due to server errors, timeouts, or rate limits. If you need to disable retries for a specific request, pass the `X-Fal-No-Retry` header when submitting:

```bash theme={null}
curl -X POST "https://queue.fal.run/fal-ai/flux/dev" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -H "X-Fal-No-Retry: 1" \
  -d '{"prompt": "a cat"}'
```

When this header is set to `1`, `true`, or `yes`, fal will not retry the request even if it fails due to a retryable error.
