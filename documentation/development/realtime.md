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

# Realtime Endpoints (WebSockets)

> Build WebSocket endpoints with @fal.realtime() for interactive apps. Send many inputs over one connection with fal_client.realtime(), or add WebRTC.

Realtime endpoints use WebSockets for bidirectional communication over a persistent connection. Once a client connects, it can send multiple inputs and receive results without the overhead of establishing new connections for each request. This makes them ideal for interactive applications like real-time image editing, live camera filters, or game-like experiences where latency between requests needs to be minimal.

You define a realtime endpoint using the `@fal.realtime("/realtime")` decorator, which uses fal's binary msgpack protocol for efficient serialization. Callers connect using the [`realtime()` method](../model-apis/inference/real-time.md) in the fal client SDKs. For one-way progressive output from a single request (like showing diffusion steps), use [Streaming Endpoints](streaming.md) instead -- they use SSE and are simpler when you don't need bidirectional communication.

> [!WARNING]
>
> WebSocket endpoints are not currently testable on the fal Playground. You can monitor your WebSocket endpoint activity through the **Logs** page in the fal dashboard.

## How Realtime Works

Under a `fal.App`, the `@fal.realtime()` decorator makes your endpoint compatible with [fal's real-time clients](../model-apis/inference/real-time.md). It uses fal's binary msgpack protocol for efficient serialization and eliminates connection establishing overhead for repeated requests.

> [!NOTE]
>
> **Important:** The `fal_client.realtime()` method connects to the `/realtime` path on your app by default. Use `@fal.realtime("/realtime")` so that clients connect without extra configuration. If your endpoint uses a different path, pass that path to the client with the `path` option. See [Custom WebSocket Path](../model-apis/inference/real-time.md#custom-websocket-path).

For power users who want to build stateful applications with their own real-time protocol, a `@fal.endpoint` can
be initialized with `is_websocket=True` flag and the underlying function will receive the raw WebSocket connection and
can choose to use it however it wants.

## Server-Side Implementation

Here's an example of a fal app with both a regular HTTP endpoint and a WebSocket endpoint:

```python theme={null}
import fal
from pydantic import BaseModel
from fastapi import WebSocket


class Input(BaseModel):
    prompt: str


class Output(BaseModel):
    output: str


class RealtimeApp(fal.App):
    keep_alive = 60

    @fal.endpoint("/")
    def generate(self, input: Input) -> Output:
        return Output(output=input.prompt)

    @fal.realtime("/realtime")
    def generate_rt(self, input: Input) -> Output:
        return Output(output=input.prompt)

    @fal.endpoint("/echo", is_websocket=True)
    async def generate_ws(self, websocket: WebSocket) -> None:
        await websocket.accept()
        msg = await websocket.receive_text()
        for idx in range(3):
            print(f"Sending message {idx}")
            await websocket.send_text(msg + f"-{idx}")
        await websocket.close()
```

### Buffering and dropped inputs

A realtime client can send inputs faster than the app processes them. Examples are webcam frames and slider updates. Use the `buffering` parameter to control the input buffer:

```python theme={null}
@fal.realtime("/realtime", buffering=2)
def generate_rt(self, input: Input) -> Output:
    ...
```

* A unary endpoint receives one input and returns one output, like `generate_rt` above. It keeps at most `buffering` unprocessed inputs for each connection. When the input buffer is full, a new input replaces the oldest input. The app always works on recent inputs. Without `buffering`, the app keeps every input and processes the inputs in order.
* All realtime endpoints use an outgoing queue that holds twice the `buffering` value, or 2 when you do not set it. When this queue is full, the app sends outputs directly. These outputs can arrive out of order.

Set a small value (1 or 2) for live inputs where only the latest input is important. Do not set `buffering` when the app must process every input.

Two related parameters control the session:

* `max_batch_size` passes up to this number of inputs from the input buffer to a unary function in one call.
* `session_timeout` ends the session when the client sends no message for this number of seconds.

## Client-Side Connection

### Connecting to `@fal.realtime()` Endpoints

For endpoints decorated with `@fal.realtime()`, use `fal_client.realtime()` or `fal_client.realtime_async()`. These methods handle serialization automatically using fal's binary protocol:

```python theme={null}
import fal_client

# Connect to a @fal.realtime() endpoint
with fal_client.realtime("your-username/your-app-name") as connection:
    connection.send({"prompt": "Hello, world!"})
    result = connection.recv()
    print(result)
```

```python theme={null}
import asyncio
import fal_client

async def main():
    async with fal_client.realtime_async("your-username/your-app-name") as connection:
        await connection.send({"prompt": "Hello, world!"})
        result = await connection.recv()
        print(result)

asyncio.run(main())
```

### Connecting to Raw WebSocket Endpoints

For endpoints using `is_websocket=True`, use `fal_client.ws_connect()` or `fal_client.ws_connect_async()` for direct WebSocket access:

```python theme={null}
import fal_client

# Connect to a raw WebSocket endpoint
# The path parameter specifies the endpoint path (e.g., "/echo")
with fal_client.ws_connect("your-username/your-app-name", path="/echo") as ws:
    ws.send("Hello, world!")
    for _ in range(3):
        message = ws.recv()
        print(message)
```

```python theme={null}
import asyncio
import fal_client

async def main():
    async with fal_client.ws_connect_async("your-username/your-app-name", path="/echo") as ws:
        await ws.send("Hello, world!")
        for _ in range(3):
            message = await ws.recv()
            print(message)

asyncio.run(main())
```

> [!NOTE]
>
> * For `@fal.realtime()` endpoints: Use `fal_client.realtime()` - serialization is handled automatically.
> * For raw `is_websocket=True` endpoints: Use `fal_client.ws_connect()` with the `path` parameter to specify the endpoint path.

> [!TIP]
>
> A shared WebSocket endpoint can declare billing on its `101` upgrade response. See [Define billable units](../serverless/publishing-to-marketplace.md#define-billable-units).

## WebRTC Transport

For applications that need direct video/audio streaming (webcam feeds, live game rendering), you can use WebRTC as the transport layer on top of fal's WebSocket infrastructure. WebRTC provides lower latency for media streams compared to sending frames over msgpack.

The low-level pattern uses a bidirectional `@fal.realtime("/realtime")` endpoint for WebRTC signaling. The endpoint receives an async iterator of signaling messages (offer, answer, and ICE candidates) and yields its replies. The media then flows between the browser and your runner over WebRTC. If no direct path is available, the media goes through a TURN relay.

```python theme={null}
@fal.realtime("/realtime", buffering=5)
async def webrtc(
    self, inputs: AsyncIterator[RealtimeInput]
) -> AsyncIterator[RealtimeOutput]:
    ...
```

For new apps, use the experimental [fal WMA (World Model Accelerator)](wma.md). It handles the signaling, ICE, tracks, and frame pacing for you. The examples below use WMA instead of this low-level pattern.

> [card] [Real-time World Model](../../examples/video-generation/deploy-realtime-world-model.md)
>
> Deploy a keyboard-controlled world model with WebRTC video streaming

> [card] [Real-time Video Style Transfer](../../examples/video-generation/deploy-realtime-video-restyle.md)
>
> Restyle a live webcam stream with a text prompt

> [card] [Real-time Voice Chat](../../examples/audio-speech/deploy-realtime-voice-chat.md)
>
> Talk to a full-duplex speech model over WebRTC audio

## Next Steps

> [card] [Streaming Endpoints](streaming.md)
>
> Stream progressive results (image previews, video updates) using SSE

> [card] [Real-time Client Docs](../model-apis/inference/real-time.md)
>
> Client library documentation for realtime connections
