> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Calling Your Endpoints

> How to call your deployed serverless endpoints using the fal client SDKs.

Once your app is deployed, you can call it using the same client SDKs and patterns used for any model on fal. Your app's endpoint ID is `your-username/your-app-name`, and all of the [inference methods](../model-apis/inference.md) work identically whether you are calling a marketplace model or your own deployed app.

This page shows quick examples of each calling pattern. `subscribe` is the simplest option since it handles polling for you and blocks until the result is ready. For production workloads where you need to manage many requests in parallel, `submit` gives you full control over the request lifecycle. For full details on parameters, response shapes, status polling, and cancellation, see the [Inference documentation](../model-apis/inference.md).

## Subscribe

Submits a request to the queue, polls automatically, and returns the result when ready. This is the simplest calling pattern since it handles the request lifecycle for you. Optionally receive progress updates via callbacks.

> [tab] Python
>
> ```python theme={null}
> import fal_client
>
> result = fal_client.subscribe("your-username/your-app-name", arguments={
>     "prompt": "a sunset over mountains"
> })
> print(result)
> ```
>
> With progress updates:
>
> ```python theme={null}
> import fal_client
>
> def on_queue_update(update):
>     if isinstance(update, fal_client.InProgress):
>         for log in update.logs:
>             print(log["message"])
>
> result = fal_client.subscribe(
>     "your-username/your-app-name",
>     arguments={"prompt": "a sunset over mountains"},
>     with_logs=True,
>     on_queue_update=on_queue_update,
> )
> ```

> [tab] JavaScript
>
> ```javascript theme={null}
> import { fal } from "@fal-ai/client";
>
> const result = await fal.subscribe("your-username/your-app-name", {
>   input: { prompt: "a sunset over mountains" },
> });
> console.log(result.data);
> ```
>
> With progress updates:
>
> ```javascript theme={null}
> import { fal } from "@fal-ai/client";
>
> const result = await fal.subscribe("your-username/your-app-name", {
>   input: { prompt: "a sunset over mountains" },
>   logs: true,
>   onQueueUpdate: (update) => {
>     if (update.status === "IN_PROGRESS") {
>       update.logs?.map((log) => console.log(log.message));
>     }
>   },
> });
> ```

> [card] [Synchronous Inference](../model-apis/inference/synchronous.md)
>
> Full details on subscribe, progress updates, and timeout handling

## Queue (Async)

For fire-and-forget workflows. Submit a request, get a request ID, and retrieve the result later by polling or webhook.

> [tab] Python
>
> ```python theme={null}
> import fal_client
>
> handler = fal_client.submit("your-username/your-app-name", arguments={
>     "prompt": "a sunset over mountains"
> })
>
> print(f"Request ID: {handler.request_id}")
>
> # Check status
> status = handler.status()
> print(status)
>
> # Get result when ready
> result = handler.get()
> print(result)
> ```

> [tab] JavaScript
>
> ```javascript theme={null}
> import { fal } from "@fal-ai/client";
>
> const { request_id } = await fal.queue.submit("your-username/your-app-name", {
>   input: { prompt: "a sunset over mountains" },
> });
>
> console.log(`Request ID: ${request_id}`);
>
> // Check status
> const status = await fal.queue.status("your-username/your-app-name", {
>   requestId: request_id,
>   logs: true,
> });
>
> // Get result when ready
> const result = await fal.queue.result("your-username/your-app-name", {
>   requestId: request_id,
> });
> ```

> [card] [Asynchronous Inference](../model-apis/inference/queue.md)
>
> Full details on the queue system, status polling, and REST API reference

## Streaming

For apps that produce progressive output via Server-Sent Events (SSE). Your app must define a streaming endpoint at `/stream` using `@fal.endpoint("/stream")`.

> [tab] Python
>
> ```python theme={null}
> import fal_client
>
> for event in fal_client.stream("your-username/your-app-name", arguments={
>     "prompt": "a sunset over mountains"
> }):
>     print(event)
> ```

> [tab] JavaScript
>
> ```javascript theme={null}
> import { fal } from "@fal-ai/client";
>
> const stream = await fal.stream("your-username/your-app-name", {
>   input: { prompt: "a sunset over mountains" },
> });
>
> for await (const event of stream) {
>   console.log(event);
> }
>
> const finalResult = await stream.done();
> ```

> [card] [Building Streaming Endpoints](streaming.md)
>
> How to implement SSE streaming in your fal.App

> [card] [Streaming Inference](../model-apis/inference/streaming.md)
>
> Client-side streaming details and REST API

## Real-Time (WebSocket)

For bidirectional, low-latency communication over a persistent connection. Your app must define a `@fal.realtime("/realtime")` endpoint.

> [tab] Python
>
> ```python theme={null}
> import fal_client
>
> with fal_client.realtime("your-username/your-app-name") as connection:
>     connection.send({"prompt": "Hello, world!"})
>     result = connection.recv()
>     print(result)
> ```
>
> Async version:
>
> ```python theme={null}
> import asyncio
> import fal_client
>
> async def main():
>     async with fal_client.realtime_async("your-username/your-app-name") as connection:
>         await connection.send({"prompt": "Hello, world!"})
>         result = await connection.recv()
>         print(result)
>
> asyncio.run(main())
> ```

> [tab] JavaScript
>
> ```javascript theme={null}
> import { fal } from "@fal-ai/client";
>
> const connection = fal.realtime.connect("your-username/your-app-name", {
>   onResult: (result) => {
>     console.log(result);
>   },
> });
>
> connection.send({ prompt: "Hello, world!" });
> ```

> [card] [Building Realtime Endpoints](realtime.md)
>
> How to implement WebSocket endpoints in your fal.App

> [card] [Real-Time Inference](../model-apis/inference/real-time.md)
>
> Client-side real-time details and proxy setup

## Webhooks

Submit a request and receive the result at a URL you specify, instead of polling.

> [tab] Python
>
> ```python theme={null}
> import fal_client
>
> handler = fal_client.submit(
>     "your-username/your-app-name",
>     arguments={"prompt": "a sunset over mountains"},
>     webhook_url="https://your-server.com/api/webhook",
> )
>
> print(f"Request ID: {handler.request_id}")
> ```

> [tab] JavaScript
>
> ```javascript theme={null}
> import { fal } from "@fal-ai/client";
>
> const { request_id } = await fal.queue.submit("your-username/your-app-name", {
>   input: { prompt: "a sunset over mountains" },
>   webhookUrl: "https://your-server.com/api/webhook",
> });
> ```

> [tab] cURL
>
> ```bash theme={null}
> curl -X POST "https://queue.fal.run/your-username/your-app-name?fal_webhook=https://your-server.com/api/webhook" \
>   -H "Authorization: Key $FAL_KEY" \
>   -H "Content-Type: application/json" \
>   -d '{"prompt": "a sunset over mountains"}'
> ```

When the request completes, fal sends a `POST` to your webhook URL with the result:

```json theme={null}
{
  "request_id": "abc-123",
  "status": "OK",
  "payload": { ... }
}
```

> [card] [Webhooks API](../model-apis/inference/webhooks.md)
>
> Full details on webhook payloads, retries, verification, and IP allowlisting

## Passing Headers

You can pass custom headers with any calling method to control platform behavior:

> [tab] Python
>
> ```python theme={null}
> import fal_client
>
> result = fal_client.subscribe(
>     "your-username/your-app-name",
>     arguments={"prompt": "a sunset"},
>     headers={
>         "x-fal-no-retry": "1",
>     },
> )
> ```

> [tab] JavaScript
>
> ```javascript theme={null}
> import { fal } from "@fal-ai/client";
>
> const result = await fal.subscribe("your-username/your-app-name", {
>   input: { prompt: "a sunset" },
>   headers: {
>     "x-fal-no-retry": "1",
>   },
> });
> ```

See [Platform Headers](../model-apis/common-parameters.md) for all available headers, and [Retries](../serverless/reliability/retries.md) for retry control. Each inference method page also documents its available SDK parameters.

## Next Steps

> [card] [Inference Documentation](../model-apis/inference.md)
>
> Full details on all calling methods, parameters, status polling, and the request handle

> [card] [Async Inference (Queue)](../model-apis/inference/queue.md)
>
> Submit, status, result, cancel, webhooks, and streaming status updates

> [card] [Client Setup](../model-apis/inference/client-setup.md)
>
> Install and configure the fal client SDK

> [card] [Handle Inputs & Outputs](handle-inputs-and-outputs.md)
>
> Define the input/output schema for your endpoints
