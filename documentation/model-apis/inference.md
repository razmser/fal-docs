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

# Inference Methods: How to Call fal Models

> Five ways to call a fal model: run() for a direct HTTP call, subscribe() and submit() through the queue, stream() over SSE, and realtime() over WebSockets.

Every model on fal can be called through the same set of inference methods, whether it is a pre-trained model from the [gallery](https://fal.ai/models) or your own app running on [Serverless](../serverless.md). This page walks through each method, explains when to reach for it, and links to the deeper reference pages. Before calling any model you will need an [API key](https://fal.ai/docs/documentation/model-apis/authentication.md) and the [fal client](inference/client-setup.md) installed in your project. To call models without an SDK, see the [Inference API reference](../../api-reference/inference-api.md).

fal provides five calling patterns that cover the spectrum from quick prototyping to high-throughput production pipelines. All of them benefit from fal's autoscaling infrastructure, where [runners](../deployment/runners.md) spin up on demand to handle your requests. The key decision is whether you need the [queue](inference/queue.md) (recommended for reliability), a direct call (simplest path), [streaming](inference/streaming.md) (progressive output), or a [real-time WebSocket](inference/real-time.md) connection (lowest latency). Each method is covered below with a code example and guidance on when it is the right fit.

> [!TIP]
>
> These calling patterns work identically for your own deployed serverless apps. Use your app's endpoint ID (e.g., `your-username/your-app-name`) instead of a model ID. See [Calling Your Endpoints](../development/calling-your-endpoints.md) for serverless-specific examples.

| Method | How it works |
| - | - |
| **`run()`** | Direct HTTP call, no queue |
| **`subscribe()`** | Queue-based, blocks until result |
| **`submit()`** | Queue-based, returns immediately (recommended) |
| **`stream()`** | Progressive output via SSE |
| **`realtime()`** | WebSocket, persistent connection |

***

## Direct (`run`)

The simplest way to call a model. Sends a direct HTTP request to `fal.run` and returns the result. No queue, no retries, no polling.

```python theme={null}
import fal_client

result = fal_client.run("fal-ai/nano-banana-2", arguments={
    "prompt": "a sunset over mountains"
})
print(result["images"][0]["url"])
```

Use `run` for quick scripts, prototyping, or any model with fast response times where you want the lowest overhead. Because there is no queue involved, the call goes straight to a runner and returns the response directly. The tradeoff is that direct calls do not retry on failure. If the runner returns an error or times out, you get the error immediately.

> [!WARNING]
>
> For production use, prefer `subscribe` or `submit` so you get automatic retries and request durability through the [queue](inference/queue.md).

> [card] [Learn more](inference/synchronous.md)
>
> Direct and queue-backed synchronous calls

***

## Subscribe (Queue-backed synchronous)

Like `run`, but uses the queue under the hood. Submits a request, polls automatically, and blocks until the result is ready. You get automatic retries and reliability with a simple interface.

```python theme={null}
import fal_client

result = fal_client.subscribe("fal-ai/nano-banana-2", arguments={
    "prompt": "a sunset over mountains"
})
print(result["images"][0]["url"])
```

`subscribe` is a good choice when you want the simplicity of a blocking call combined with queue-backed reliability. It handles polling for you, so the code looks almost identical to `run`, but your request is durable and will be retried if a runner fails. Reach for it in simple integrations, backend scripts, or anywhere you do not need to manage the request lifecycle yourself.

> [card] [Learn more](inference/synchronous.md)
>
> Direct and queue-backed synchronous calls

***

## Asynchronous (`submit`)

The recommended approach for production. Submit a request to the [queue](inference/queue.md) and return immediately, then poll for status or receive results via [webhook](inference/webhooks.md).

**Polling:**

```python theme={null}
import time
import fal_client

handler = fal_client.submit("fal-ai/nano-banana-2", arguments={
    "prompt": "a sunset over mountains"
})

while True:
    status = handler.status()

    if isinstance(status, fal_client.Queued):
        print(f"In queue, position: {status.position}")
    elif isinstance(status, fal_client.InProgress):
        print("Processing...")
    elif isinstance(status, fal_client.Completed):
        print(f"Done in {status.metrics.get('inference_time', '?')}s")
        break

    time.sleep(0.5)

result = handler.get()
print(result["images"][0]["url"])
```

### Status types

The `handler.status()` method returns one of three types. Status updates are available without request logs.

Request logs are available only to the app owner. Non-owner callers receive no request logs. For your own deployed apps, see [Logging](../development/logging.md#request-logs).

| Type | Fields | Meaning |
| - | - | - |
| `Queued` | `position` (int) | Waiting in queue. `position` is how many requests are ahead. |
| `InProgress` | `logs` (list or None) | A runner is processing the request. Non-owner callers receive no request logs. |
| `Completed` | `logs` (list or None), `metrics` (dict) | Result is ready. `metrics` includes `inference_time` in seconds. |

In JavaScript, the equivalent types are `InQueueQueueStatus`, `InProgressQueueStatus`, and `CompletedQueueStatus`. See the full [Python SDK reference](../../api-reference/client-libraries/python/fal_client.md#status) and [JavaScript SDK reference](../../api-reference/client-libraries/javascript/types.common.md) for details.

**Webhook (no polling needed):**

```python theme={null}
import fal_client

handler = fal_client.submit("fal-ai/nano-banana-2",
    arguments={"prompt": "a sunset over mountains"},
    webhook_url="https://your-server.com/api/webhook"
)
```

Async inference is the recommended path for production because it works reliably at any scale. You can submit many requests in parallel, process them as they complete, and track queue position and request status. The queue handles retries and request persistence automatically through the [reliability layer](inference/reliability.md).

> [card] [Learn Async Inference](inference/queue.md)
>
> The recommended way to call models at scale

***

## Streaming (`stream`)

For models that produce output progressively. Each event arrives as it is generated, so you can display partial results without waiting for the full response. This is useful for showing image generation previews or streaming LLM tokens.

```python theme={null}
import fal_client

for event in fal_client.stream("fal-ai/flux/schnell", arguments={
    "prompt": "a sunset over mountains"
}):
    print(event)
```

> [!NOTE]
>
> The `stream()` method connects to the `/stream` path on the model endpoint. Not all models support streaming. Check the model's API documentation for availability.

> [card] [Learn Streaming](inference/streaming.md)
>
> Receive output as it's generated

***

## Real-time (`realtime`)

For interactive applications that need the lowest possible latency. Opens a persistent WebSocket connection to a warm runner, enabling back-to-back requests without reconnection overhead. Only available for models with an explicit real-time endpoint.

```python theme={null}
import fal_client

with fal_client.realtime("fal-ai/fast-sdxl") as connection:
    connection.send({
        "prompt": "a sunset over mountains",
        "num_inference_steps": 2
    })
    result = connection.recv()
    print(result)
```

> [card] [Learn Real-time](inference/real-time.md)
>
> WebSocket connections for interactive apps

***

## Getting Started

Before calling any model, install and configure the fal client for your language. If you are building a browser-based app, you will also need a server-side proxy to keep your API key out of client-side code.

> [card] [Client Setup](inference/client-setup.md)
>
> Install and configure the fal client

> [card] [Proxy Setup](inference/proxy-setup.md)
>
> Keep your API key secure in client-side apps
