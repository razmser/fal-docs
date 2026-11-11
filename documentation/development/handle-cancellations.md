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

# Handle Request Cancellations

> Handle in-flight request cancellations to free GPU resources when callers cancel queued or processing requests.

When a caller [cancels a request](../model-apis/inference/queue.md#cancel-a-request) through the queue API, fal handles it differently depending on the request's state. If the request is still waiting in the [queue](../model-apis/inference/queue.md), it is removed and never processed. If a runner is already processing the request, fal sends a cancellation signal to your app so you can stop work, release GPU resources, and leave the app in a clean state.

For direct connections ([`run()`](../model-apis/inference/synchronous.md), [streaming](../model-apis/inference/streaming.md), and [real-time](../model-apis/inference/real-time.md)), cancellation also triggers automatically when the caller drops the HTTP or WebSocket connection -- for example, if their process crashes or the connection times out. This does not apply to queue-based requests ([`submit()`](../model-apis/inference/queue.md), [`subscribe()`](../model-apis/inference/synchronous.md)), where the connection closes after submission and only an explicit cancel API call can stop the request.

Without cancellation handling, a cancelled request continues consuming GPU time until it finishes naturally. For long-running operations like video generation or training, this can waste significant compute. By implementing a cancel endpoint, you give fal a way to tell your running code to stop early, so the runner can move on to the next request.

## How Cancellation Works

fal routes cancellation requests to your endpoint's path with a `/cancel` suffix. For an endpoint at `/predict`, the cancellation arrives at `/predict/cancel`. Both the original request and the cancellation carry the same `x-fal-request-id` header, which you use to match the cancellation to the right in-flight task.

**1. Caller submits a request**

fal assigns a `request_id` and routes the request to a runner. The caller receives the request handle.

```python Python theme={null}
handler = fal_client.submit("your-username/your-app", arguments={"prompt": "a sunset"})
print(handler.request_id)
```

```javascript JavaScript theme={null}
const { request_id } = await fal.queue.submit("your-username/your-app", {
  input: { prompt: "a sunset" },
});
```

**2. Your endpoint begins processing**

The runner calls your endpoint. You wrap the work in an `asyncio.Task` and store it by request ID so the cancel endpoint can find it later.

```python focus={9-19} theme={null}
class MyApp(fal.App):
    def setup(self):
        self._tasks: dict[str, asyncio.Task] = {}

    async def _generate(self, input: GenerateInput):
        await asyncio.sleep(30)
        return {"result": f"Generated from: {input.prompt}"}

    @fal.endpoint("/predict")
    async def predict(
        self,
        input: GenerateInput,
        x_fal_request_id: str | None = fastapi.Header(None),
    ):
        if not x_fal_request_id:
            raise ValueError("x-fal-request-id is required")

        task = asyncio.create_task(self._generate(input))
        self._tasks[x_fal_request_id] = task
        try:
            return await task
        except asyncio.CancelledError:
            print(f"Request {x_fal_request_id} was cancelled")
            raise RequestCancelledException("Request cancelled")
        finally:
            self._tasks.pop(x_fal_request_id, None)

    @fal.endpoint("/predict/cancel")
    async def predict_cancel(
        self,
        x_fal_request_id: str | None = fastapi.Header(None),
    ):
        if not x_fal_request_id:
            raise ValueError("x-fal-request-id is required")

        task = self._tasks.get(x_fal_request_id)
        if task is None:
            raise ValueError("No task found for this request ID")

        if task.done():
            raise ValueError("Task has already completed")

        task.cancel()
```

**3. Caller cancels the request**

The caller decides to cancel -- either via the SDK, the REST API, or by disconnecting (for direct connections only).

```python Python theme={null}
handler.cancel()
```

```javascript JavaScript theme={null}
await fal.queue.cancel("your-username/your-app", {
  requestId: request_id,
});
```

```bash cURL theme={null}
curl -X PUT "https://queue.fal.run/your-username/your-app/requests/{request_id}/cancel" \
  -H "Authorization: Key $FAL_KEY"
```

**4. fal calls your cancel endpoint**

fal tracks which endpoint path each request was sent to. When a cancel arrives, it looks up the original path and appends `/cancel`. So if the request was routed to `/predict`, fal sends the cancellation to `/predict/cancel` on the same runner, with the same `x-fal-request-id` header. Your cancel endpoint looks up the task and cancels it.

```python focus={28-43} theme={null}
class MyApp(fal.App):
    def setup(self):
        self._tasks: dict[str, asyncio.Task] = {}

    async def _generate(self, input: GenerateInput):
        await asyncio.sleep(30)
        return {"result": f"Generated from: {input.prompt}"}

    @fal.endpoint("/predict")
    async def predict(
        self,
        input: GenerateInput,
        x_fal_request_id: str | None = fastapi.Header(None),
    ):
        if not x_fal_request_id:
            raise ValueError("x-fal-request-id is required")

        task = asyncio.create_task(self._generate(input))
        self._tasks[x_fal_request_id] = task
        try:
            return await task
        except asyncio.CancelledError:
            print(f"Request {x_fal_request_id} was cancelled")
            raise RequestCancelledException("Request cancelled")
        finally:
            self._tasks.pop(x_fal_request_id, None)

    @fal.endpoint("/predict/cancel")
    async def predict_cancel(
        self,
        x_fal_request_id: str | None = fastapi.Header(None),
    ):
        if not x_fal_request_id:
            raise ValueError("x-fal-request-id is required")

        task = self._tasks.get(x_fal_request_id)
        if task is None:
            raise ValueError("No task found for this request ID")

        if task.done():
            raise ValueError("Task has already completed")

        task.cancel()
```

**5. Your main endpoint handles the cancellation**

Back in step 2, the `await task` now raises `CancelledError`. Your main endpoint catches it and raises `RequestCancelledException` to signal to fal that the cancellation was handled cleanly (returns a 499 status code). The `finally` block cleans up the task reference regardless of outcome.

```python focus={20-26} theme={null}
class MyApp(fal.App):
    def setup(self):
        self._tasks: dict[str, asyncio.Task] = {}

    async def _generate(self, input: GenerateInput):
        await asyncio.sleep(30)
        return {"result": f"Generated from: {input.prompt}"}

    @fal.endpoint("/predict")
    async def predict(
        self,
        input: GenerateInput,
        x_fal_request_id: str | None = fastapi.Header(None),
    ):
        if not x_fal_request_id:
            raise ValueError("x-fal-request-id is required")

        task = asyncio.create_task(self._generate(input))
        self._tasks[x_fal_request_id] = task
        try:
            return await task
        except asyncio.CancelledError:
            print(f"Request {x_fal_request_id} was cancelled")
            raise RequestCancelledException("Request cancelled")
        finally:
            self._tasks.pop(x_fal_request_id, None)

    @fal.endpoint("/predict/cancel")
    async def predict_cancel(
        self,
        x_fal_request_id: str | None = fastapi.Header(None),
    ):
        if not x_fal_request_id:
            raise ValueError("x-fal-request-id is required")

        task = self._tasks.get(x_fal_request_id)
        if task is None:
            raise ValueError("No task found for this request ID")

        if task.done():
            raise ValueError("Task has already completed")

        task.cancel()
```

> [!NOTE]
>
> Cancellation is cooperative. If your task is blocked on a synchronous GPU operation (like a PyTorch inference call), the cancellation won't take effect until the operation completes and Python checks for the cancel signal. For long-running synchronous operations, consider breaking them into checkpoints where you can check for cancellation.

`RequestCancelledException` is a special exception from `fal.exceptions` that returns a 499 status code to fal, signaling that the cancellation was handled cleanly. This tells the gateway that the runner is in a good state and can accept new requests. If you let `CancelledError` propagate unhandled instead, FastAPI returns a 500 error, which does not terminate the runner but does not cleanly signal that cancellation was handled either.

## Full Example

```python theme={null}
import asyncio

import fal
from fal.exceptions import RequestCancelledException
import fastapi
from pydantic import BaseModel


class GenerateInput(BaseModel):
    prompt: str


class MyApp(fal.App):
    def setup(self):
        self._tasks: dict[str, asyncio.Task] = {}

    async def _generate(self, input: GenerateInput):
        await asyncio.sleep(30)
        return {"result": f"Generated from: {input.prompt}"}

    @fal.endpoint("/predict")
    async def predict(
        self,
        input: GenerateInput,
        x_fal_request_id: str | None = fastapi.Header(None),
    ):
        if not x_fal_request_id:
            raise ValueError("x-fal-request-id is required")

        task = asyncio.create_task(self._generate(input))
        self._tasks[x_fal_request_id] = task
        try:
            return await task
        except asyncio.CancelledError:
            print(f"Request {x_fal_request_id} was cancelled")
            raise RequestCancelledException("Request cancelled")
        finally:
            self._tasks.pop(x_fal_request_id, None)

    @fal.endpoint("/predict/cancel")
    async def predict_cancel(
        self,
        x_fal_request_id: str | None = fastapi.Header(None),
    ):
        if not x_fal_request_id:
            raise ValueError("x-fal-request-id is required")

        task = self._tasks.get(x_fal_request_id)
        if task is None:
            raise ValueError("No task found for this request ID")

        if task.done():
            raise ValueError("Task has already completed")

        task.cancel()
```
