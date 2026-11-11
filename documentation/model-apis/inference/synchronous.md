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

# Synchronous Inference

> A convenience wrapper for simple blocking calls

There are two ways to make a simple blocking call to a model on fal. `run` sends a direct HTTP request to `fal.run` and returns the result immediately, with no queue involved. `subscribe` uses the [queue](queue.md) under the hood but handles polling automatically, so it feels just as simple while giving you automatic retries and reliability.

Both are good starting points for scripts, prototyping, and any situation where you just want the output without managing the request lifecycle yourself. Use `run` when you want the fastest possible path with no overhead, and `subscribe` when you want queue-backed reliability. For production workloads that need parallel processing or [webhooks](webhooks.md), use [async inference](queue.md) instead. To call `fal.run` without an SDK, see the [Synchronous API reference](../../../api-reference/inference-api/sync/run.md).

## Using `run` (Direct)

`run` sends a direct HTTP request to `fal.run`. There is no queue and no status polling. The response comes back in the same HTTP connection. The SDK retries on transient HTTP errors (like 502/503/504), but there are no server-side queue retries.

```python Python theme={null}
import fal_client

result = fal_client.run("fal-ai/flux/schnell", arguments={
    "prompt": "a sunset over mountains"
})

print(result["images"][0]["url"])
```

```python Python (async) theme={null}
import asyncio
import fal_client

async def main():
    result = await fal_client.run_async("fal-ai/flux/schnell", arguments={
        "prompt": "a sunset over mountains"
    })
    print(result["images"][0]["url"])

asyncio.run(main())
```

```javascript JavaScript theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.run("fal-ai/flux/schnell", {
  input: { prompt: "a sunset over mountains" }
});

console.log(result.data.images[0].url);
```

## Using `subscribe` (Queue-backed)

`subscribe` submits to the queue and polls until the result is ready. You get automatic retries, timeout handling, and scaling, with the same simple blocking interface as `run`.

```python Python theme={null}
import fal_client

result = fal_client.subscribe("fal-ai/flux/schnell", arguments={
    "prompt": "a sunset over mountains"
})

print(result["images"][0]["url"])
```

```python Python (async) theme={null}
import asyncio
import fal_client

async def main():
    result = await fal_client.subscribe_async("fal-ai/flux/schnell", arguments={
        "prompt": "a sunset over mountains"
    })
    print(result["images"][0]["url"])

asyncio.run(main())
```

```javascript JavaScript theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/flux/schnell", {
  input: { prompt: "a sunset over mountains" }
});

console.log(result.data.images[0].url);
```

## With Status Updates

Use the queue-update callback to track when a request is queued or processing. This does not require access to request logs.

```python Python theme={null}
import fal_client

def on_queue_update(update):
    if isinstance(update, fal_client.Queued):
        print(f"Queue position: {update.position}")
    elif isinstance(update, fal_client.InProgress):
        print("Processing...")

result = fal_client.subscribe(
    "fal-ai/flux/schnell",
    arguments={"prompt": "a sunset over mountains"},
    on_queue_update=on_queue_update
)
```

```python Python (async) theme={null}
import asyncio
import fal_client

async def main():
    def on_queue_update(update):
        if isinstance(update, fal_client.Queued):
            print(f"Queue position: {update.position}")
        elif isinstance(update, fal_client.InProgress):
            print("Processing...")

    result = await fal_client.subscribe_async(
        "fal-ai/flux/schnell",
        arguments={"prompt": "a sunset over mountains"},
        on_queue_update=on_queue_update
    )
    print(result["images"][0]["url"])

asyncio.run(main())
```

```javascript JavaScript theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/flux/schnell", {
  input: { prompt: "a sunset over mountains" },
  onQueueUpdate: (update) => {
    if (update.status === "IN_QUEUE") {
      console.log(`Queue position: ${update.queue_position}`);
    } else if (update.status === "IN_PROGRESS") {
      console.log("Processing...");
    }
  }
});
```

## `run()` Parameters

> [!NOTE]
>
> In JavaScript, `fal.run()` does not currently support `timeout`, `hint`, or `headers`. It does support `startTimeout`. To use `hint`, `headers`, or `priority` from JavaScript, use [`fal.queue.submit()`](queue.md#submit-parameters) or [`fal.subscribe()`](#subscribe-parameters).

### `path`

Endpoint path appended to the model ID. Leave empty for the default root endpoint. See [`path` reference](queue.md#path).

```python Python theme={null}
result = fal_client.run("fal-ai/nano-banana-2", arguments={...}, path="/custom-endpoint")
```

```javascript JavaScript theme={null}
const result = await fal.run("fal-ai/nano-banana-2/custom-endpoint", {
  input: {...},
});
```

In JavaScript, append the path directly to the endpoint string.

### `timeout`

Client-side HTTP timeout in seconds (Python only). This is a standard HTTP connection timeout -- how long the client library waits for the server to send back a complete response. If the connection is idle for longer than this, the client raises a timeout error. It does not affect the server.

```python theme={null}
result = fal_client.run("fal-ai/nano-banana-2", arguments={...}, timeout=120)
```

### `start_timeout`

Server-side deadline covering total elapsed time until a runner successfully starts processing. Returns 504 if exceeded. See [`start_timeout` reference](queue.md#start-timeout) for full details on how the clock behaves across retries.

```python Python theme={null}
result = fal_client.run("fal-ai/nano-banana-2", arguments={...}, start_timeout=30)
```

```javascript JavaScript theme={null}
const result = await fal.run("fal-ai/nano-banana-2", {
  input: {...},
  startTimeout: 30,
});
```

### `hint`

Routing hint for session affinity -- routes requests to the same runner. See [`hint` reference](queue.md#hint).

```python Python theme={null}
result = fal_client.run("fal-ai/nano-banana-2", arguments={...}, hint="user-session-abc")
```

```javascript JavaScript theme={null}
// fal.run() does not support hint -- use fal.queue.submit() instead
const { request_id } = await fal.queue.submit("fal-ai/nano-banana-2", {
  input: {...},
  hint: "user-session-abc",
});
```

### `headers`

Additional HTTP headers for [platform-level controls](../common-parameters.md) like disabling retries or payload storage. See [`headers` reference](queue.md#headers).

```python Python theme={null}
result = fal_client.run("fal-ai/nano-banana-2", arguments={...}, headers={"X-Fal-No-Retry": "1"})
```

```javascript JavaScript theme={null}
// fal.run() does not support headers -- use fal.subscribe() instead
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: {...},
  headers: { "X-Fal-No-Retry": "1" },
});
```

***

## `subscribe()` Parameters

`subscribe()` accepts all the parameters above except `timeout`, plus the queue-specific options below. For shared parameters (`path`, `start_timeout`, `hint`, `headers`, `priority`), see the [`submit()` reference](queue.md#submit-parameters). In JavaScript, `fal.subscribe()` supports `priority`, `headers`, and `webhookUrl` as named options; for `hint`, use [`fal.queue.submit()`](queue.md#hint) directly.

### `client_timeout` / `timeout`

Client-side total deadline that limits the total time `subscribe()` blocks, including queue wait and processing. When exceeded, the client stops polling and raises an error. The request may still be processing on the server after the client gives up. In Python the parameter is called `client_timeout` and accepts seconds. In JavaScript the parameter is called `timeout` and accepts milliseconds.

If you set `client_timeout` without setting `start_timeout`, the Python SDK automatically sets `start_timeout = client_timeout` so the server also respects your deadline. If you set `start_timeout` larger than `client_timeout`, the SDK emits a warning.

```python Python theme={null}
result = fal_client.subscribe(
    "fal-ai/nano-banana-2",
    arguments={"prompt": "a sunset"},
    client_timeout=60,
)
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset" },
  timeout: 60000,  // milliseconds
});
```

For a comparison of all timeout mechanisms, see [Timeouts and Retries](../../serverless/reliability/retries.md#timeouts-and-retries).

### `with_logs` / `logs`

This option requests logs in the queue status updates that `subscribe()` retrieves. It is named `with_logs` in Python and `logs` in JavaScript.

Only the app owner can receive request logs. Non-owner callers receive empty log arrays, even when this option is enabled.

Use it to debug [your own deployed app](../../development/logging.md#request-logs). It does not provide generation progress when you call someone else's model.

```python Python theme={null}
result = fal_client.subscribe(
    "your-username/your-app",
    arguments={"prompt": "a sunset"},
    with_logs=True,
)
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("your-username/your-app", {
  input: { prompt: "a sunset" },
  logs: true,
});
```

### `on_enqueue`

A callback function that fires once, immediately after the request enters the queue. It receives the `request_id` as its argument. Use this to store the request ID for later reference -- for example, to display it in a UI or save it to a database so you can retrieve the result later even if the client disconnects.

```python Python theme={null}
def save_request_id(request_id):
    print(f"Enqueued: {request_id}")

result = fal_client.subscribe(
    "fal-ai/nano-banana-2",
    arguments={"prompt": "a sunset"},
    on_enqueue=save_request_id,
)
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset" },
  onEnqueue: (requestId) => {
    console.log(`Enqueued: ${requestId}`);
  },
});
```

### `on_queue_update` / `onQueueUpdate`

A callback function that fires each time the client polls for status. It receives a status object with a `status` field indicating the current state.

In Python, the status is one of three types: `Queued` (has `position`), `InProgress` (has `logs`), or `Completed` (has `logs` and `metrics`). In JavaScript, check the `status` string field: `"IN_QUEUE"`, `"IN_PROGRESS"`, or `"COMPLETED"`.

```python Python theme={null}
def on_update(update):
    if isinstance(update, fal_client.Queued):
        print(f"Queue position: {update.position}")
    elif isinstance(update, fal_client.InProgress):
        print("Processing...")

result = fal_client.subscribe(
    "fal-ai/nano-banana-2",
    arguments={"prompt": "a sunset"},
    on_queue_update=on_update,
)
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset" },
  onQueueUpdate: (update) => {
    if (update.status === "IN_QUEUE") {
      console.log(`Position: ${update.queue_position}`);
    } else if (update.status === "IN_PROGRESS") {
      console.log("Processing...");
    }
  },
});
```

## When to Use

Synchronous methods are best for simple scripts, prototyping, and any situation where you just want the result without managing the request lifecycle. Use `run` when you want the fastest path with no overhead. Use `subscribe` when you want queue-backed reliability with automatic retries.

For production workloads that need parallel request processing, webhooks, or full visibility into queue status, use [async inference](queue.md) instead.

## Error Responses

When a request fails due to infrastructure issues (timeouts, runner errors, etc.), the response includes a JSON body with `detail` and `error_type` fields, along with an `X-Fal-Error-Type` header. For the full list of error types and their meanings, see [Request Error Types](../request-errors.md).

For model-level validation errors (invalid inputs, content policy violations, etc.), see [Model Errors](../errors.md).
