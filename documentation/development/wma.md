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

# fal WMA (World Model Accelerator)

> Deploy a Python WebRTC app, describe its realtime interface, and connect through the fal playground or your browser application.

fal WMA (World Model Accelerator) connects your application to a fal runner through WebRTC. A session can exchange control messages, audio, and video.
Use `fal.wma.App` to manage the session and `fal.wma.AiortcPeer` to connect a Python media pipeline.

> [!WARNING]
>
> WMA is experimental, including when it ships in a stable `fal` package. Pin
> your SDK versions because the WMA API can change. The Python examples require
> `fal` 1.81.4 or later and Pydantic 2. Endpoint billing declarations require
> `fal` 1.82.0 or later. This page pins `fal` 1.82.1. The
> [browser walkthrough](wma-browser.md) uses
> `@fal-ai/client@1.11.0-alpha.5`. The stable `@fal-ai/client` release does not
> include WMA support yet.

## Deploy your first app

Start with the generated-video example. It runs on a CPU and requires no camera, model weights, or GPU.
You can change its colors and speed while the video streams.

### 1. Install and authenticate

Use Python 3.12 for this walkthrough. Create a virtual environment, then install the SDK:

```bash theme={null}
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install 'fal[wma]==1.82.1' 'pydantic>=2,<3'
fal auth login
```

On Windows PowerShell, activate the environment with `.venv\Scripts\Activate.ps1`.
If your package index cannot find `1.82.1`, wait for the PyPI publishing workflow to finish.
A GitHub release can exist before its package is available on PyPI.

The `wma` extra installs local WebRTC dependencies. WMA apps also receive `aiortc` in their runner requirements.
Your account needs deployment access and WMA bridge access. Installing the package does not grant either permission.

### 2. Download and deploy the video example

Download the example from the matching SDK release:

```bash theme={null}
curl --fail --location \
  https://raw.githubusercontent.com/fal-ai/fal/fal_v1.82.1/projects/fal/examples/wma_video.py \
  --output wma_video.py
fal deploy wma_video.py::VideoApp --auth private
```

Keep the application ID from the deployment output. You will use this ID in the playground and browser client.
The example uses an XS CPU runner, permits two simultaneous sessions per runner, and limits each session to two minutes.
Runner compute can incur charges. Disconnect when you finish testing.

### 3. Connect in the playground

1. Open your deployed app in the fal dashboard.
2. Select its `/start-session` playground.
3. Select **Connect** and wait for the video.
4. Select **Fast sunset**, then select **Send message**.
5. Confirm that the colors and speed change.
6. Select **Disconnect** to end the session.

The playground displays an AsyncAPI link and controls for the declared messages.
If you see an SDP text field instead, check the [realtime contract](#describe-your-app-for-the-playground).

## Choose an example

Each example includes a realtime contract and deployment requirements. Download the file, then deploy the named class with `--auth private`.

| Example | Class | What you can test |
| - | - | - |
| [Control echo](https://github.com/fal-ai/fal/blob/fal_v1.82.1/projects/fal/examples/wma_echo.py) | `EchoApp` | Send a text message and receive it back. |
| [Generated video](https://github.com/fal-ai/fal/blob/fal_v1.82.1/projects/fal/examples/wma_video.py) | `VideoApp` | Change a video stream's palette and speed without a camera. |
| [Camera effects](https://github.com/fal-ai/fal/blob/fal_v1.82.1/projects/fal/examples/wma_filter.py) | `FilterApp` | Send camera video and receive mirrored, monochrome, or edge-filtered video. |
| [Motion measurements](https://github.com/fal-ai/fal/blob/fal_v1.82.1/projects/fal/examples/wma_motion.py) | `MotionApp` | Send camera video and receive brightness and motion measurements as messages. |

The camera examples need browser camera permission. Motion measurements use pixel differences, not an object detector.
For your own frontend, follow [Connect a browser to fal WMA](wma-browser.md).

## Build your own app

Subclass `fal.wma.App` and implement `create_backend(session)`. Return an `AiortcPeer`, or implement `PeerBackend` for your own transport.
The following complete app echoes typed control messages. Save it as `wma_echo.py`.

```python theme={null}
from typing import Literal

from pydantic import BaseModel, Field

import fal.wma


class EchoMessage(BaseModel):
    type: Literal["echo"] = "echo"
    text: str = Field(default="Hello, WMA!", description="Text to echo back.")


class EchoApp(fal.wma.App):
    machine_type = "XS"
    requirements = ["pydantic>=2,<3"]
    max_multiplexing = 4
    secrets = ["FAL_KEY"]
    realtime_contract = fal.wma.RealtimeContract(
        media=fal.wma.MediaContract(),
        client_messages=EchoMessage,
        server_messages=EchoMessage,
        client_message_examples=(
            fal.wma.MessageExample(
                name="Say hello",
                payload={"type": "echo", "text": "Hello, WMA!"},
            ),
        ),
    )

    async def create_backend(self, session):
        from aiortc import RTCConfiguration

        servers, _ = await fal.wma.RunnerIceConfig.from_bridge().build_ice_servers_async(
            session.offer.ice_servers,
            forwarded_status=session.offer.ice_status,
        )
        session.on_message("echo", lambda message: session.send(message))
        return fal.wma.AiortcPeer(
            session,
            lambda _pc: None,
            rtc_configuration=RTCConfiguration(iceServers=servers),
        )
```

Deploy it with:

```bash theme={null}
fal deploy wma_echo.py::EchoApp --auth private
```

The echo handler sends back the received message. Contract schemas describe your API but do not validate incoming messages automatically.
For application commands, validate the payload inside your handler with your Pydantic model before you use it.

The callback passed to `AiortcPeer` receives the peer connection. Attach outgoing tracks with `pc.addTrack(track)`.
Register a `pc.on("track")` handler to process incoming media. The video and camera examples demonstrate both patterns.

## Describe your app for the playground

Declare `realtime_contract` on every app that needs the WMA playground.
The SDK generates AsyncAPI and links it from the `/start-session` OpenAPI operation through `x-fal-realtime`.
This metadata enables Connect/Disconnect controls, message forms, examples, and media previews.

Use Pydantic models for `client_messages` and `server_messages`. Add `MessageExample` entries for commands that customers can try.
Use `MediaContract` to describe the actual media tracks. Track directions are always from the client's perspective:

| App | `MediaContract.send` | `MediaContract.receive` |
| - | - | - |
| Control echo | Empty | Empty |
| Generated video | Empty | `Track(kind="video")` |
| Camera effects | `Track(kind="video", source="camera", required=True)` | `Track(kind="video")` |
| Motion measurements | `Track(kind="video", source="camera", required=True)` | Empty |

For example, a camera-to-video app declares:

```python theme={null}
media = fal.wma.MediaContract(
    send=(fal.wma.Track(kind="video", source="camera", required=True),),
    receive=(fal.wma.Track(kind="video"),),
)
```

Redeploy after changing the contract, then refresh the playground.
Transport can work without a contract, but the SDK cannot infer your messages or tracks.
Core sessions support Pydantic 1. Typed contracts and schema rendering require Pydantic 2.

## Declare client interactions

A contract lists your messages. An interaction profile tells clients how to use those messages during a session.
Set `RealtimeContract.interaction` to one profile from `fal.wma.profile`. The SDK publishes it in AsyncAPI as `x-fal-wma` version 0.1.
A profile describes your existing protocol. It does not run the protocol or validate messages at runtime.

| Profile | Use it when |
| - | - |
| `CommandWorld` | The world starts automatically and the client sends held and pressed commands. |
| `ConfiguredSession` | The client sends one setup message, waits for acknowledgment, then sends text. |

A `CommandWorld` app must declare a received video track. The first video frame tells the client that the world is ready.
Each command field must be an optional list of literal strings with no minimum length. If you declare a pressed field, it must use the same values as the held field.

The following contract declares a world with four movement commands and a reset action:

```python theme={null}
from typing import Annotated, List, Literal, Union

from pydantic import BaseModel, Field

import fal.wma
from fal.wma.profile import CommandState, CommandWorld, SerialAction

Command = Literal["forward", "back", "left", "right"]


class Move(BaseModel):
    type: Literal["move"] = "move"
    held: List[Command] = Field(default_factory=list)
    pressed: List[Command] = Field(default_factory=list)


class Restart(BaseModel):
    type: Literal["restart"] = "restart"


class Restarted(BaseModel):
    type: Literal["restarted"] = "restarted"


contract = fal.wma.RealtimeContract(
    media=fal.wma.MediaContract(receive=(fal.wma.Track(kind="video"),)),
    client_messages=Annotated[Union[Move, Restart], Field(discriminator="type")],
    server_messages=Restarted,
    interaction=CommandWorld(
        commands=CommandState(Move, "/held", "/pressed"),
        reset=SerialAction(Restart, Restarted),
    ),
)
```

`CommandState` takes the message model and JSON Pointers to its held and pressed fields. Each held report is the complete current state.
`SerialAction` pairs a client message with its acknowledgment. The client sends one setup, reset, or text action at a time and waits for its acknowledgment before it sends the next.
`CommandWorld` also accepts `setup`, `updates`, `errors`, `stop`, and `ended` declarations.

`ConfiguredSession` declares a `setup` message, a `ready` event, a client `Sequence`, and `VersionedInput` text updates.
Each update carries a sequence number. Your app correlates its pending, applied, and rejected events to that number.
The SDK refuses to publish a profile that refers to messages or fields that are not in the contract.

For keyboard input, create `fal.wma.KeyState(key_order)` with upper-case key names.
Pass each client report to `update(pressed, activated)`, where `pressed` lists held keys and `activated` lists tapped keys.
Call `sample()` once for each generation step. A tapped key acts for one step. A held key acts until the client releases it.

## Session lifecycle and cleanup

The client sends its offer through the WMA bridge. The bridge forwards it to your app's `POST /start-session` endpoint.
The response streams the answer and keeps the HTTP connection open for the session's lifetime.
Media and control messages travel through WebRTC, either directly or through TURN.

Closing the session closes its backend and finalizes deferred usage. Do not add application heartbeat or close routes for this lifecycle.
Keep mutable state inside `create_backend` so different sessions do not share controls accidentally.
Use `session.create_task()` for background work that must stop with the session.
Bind resources with `session.bind_backend(backend)` before setup work that can fail.

`AiortcPeer` stops its attached outgoing tracks during cleanup. Use a separate relay subscription when sessions share a media source.
It limits the initial connection wait to 35 seconds. Set `disconnected_grace_seconds` if you want a bounded wait after a transient disconnect.

`session.send(message)` returns `False` when the channel cannot accept a message or its buffer is full.
Drop stale updates or retry later. A worker thread's `True` result confirms queue acceptance, not delivery to the client.

Use these settings to control connection and billing behavior:

| Setting | Set on | Default | Behavior |
| - | - | - | - |
| `initial_connect_timeout_seconds` | `AiortcPeer` | `35` | Ends the session if the peer does not connect in time. Set `None` to wait without a limit. |
| `disconnected_grace_seconds` | `AiortcPeer` | `None` | Ends the session after this wait in the disconnected state. `0` ends it at once. |
| `minimum_billable_units` | `App` class | `0` | Sets the minimum units that a session bills when it closes after setup succeeds. |
| `billing_debug` | `App` class | `False` | Logs one `[wma-billing]` line for each billing event in each session. |

With `disconnected_grace_seconds` set to `None`, a disconnected peer stays open until the connection fails or closes.
The control channel holds at most 1 MiB of outgoing data. Worker threads can queue at most 64 messages.

## ICE, billing, and deployment

Use `RunnerIceConfig.from_bridge()` with the forwarded offer, as shown above. It validates the supplied ICE servers before use.
TURN permits connections on networks where a direct path fails. A successful local connection does not prove that TURN works.

For metered application usage, call `session.add_billable_units(n)` for delivered work.
Use `session.cap_billable_units(n)` before finalization if cleanup identifies undelivered work.
Deferred usage is zero on setup failures by default. Runner compute can still incur charges.
Billing report failures require operational follow-up.

Set `minimum_billable_units` to bill a minimum for each session that completes setup, for example after an early disconnect.
Setup failures and calls that do not come through fal still bill zero.
Set `billing_debug = True` to trace billing events in the runner logs. Leave it off in normal operation.

The base app includes the `FAL_KEY` secret for its billing reporter. Preserve it if your subclass overrides `secrets`.
Configure the required [runner secrets](manage-secrets-securely.md) and endpoint pricing before using deferred billing.
Your browser application's API key belongs on its server, never in browser code.

Before customer handoff, verify these behaviors against the deployed app:

* Connect with the intended client and network.
* Exchange the declared controls and media.
* Test TURN on a network that requires relay connectivity.
* Disconnect and reconnect with a new session.
* Confirm that cleanup stops background work and releases resources.
* Confirm the settled billing amount for delivered work.

### Declare billing components

A WMA app can serve other HTTP endpoints next to `/start-session`. Each of these endpoints can declare the billable components that it reports.
The SDK publishes each declaration for fal. A declaration does not report usage or create charges.
Pass an object with a `declaration()` method to `@fal.endpoint(..., billing=...)`.
The method returns a `fal.BillingComponents` dictionary:

```python theme={null}
from dataclasses import dataclass

import fal


@dataclass(frozen=True)
class FrameBilling:
    def declaration(self) -> fal.BillingComponents:
        return {
            "version": 1,
            "schema_id": "frames-v1",
            "app": {
                "type": "object",
                "properties": {"frames": {"type": "integer", "minimum": 0}},
            },
        }
```

Then attach it to an endpoint with `@fal.endpoint("/render", billing=FrameBilling())`.
The `app` value is a JSON Schema object that names the billable metrics.
Deploy metadata publishes each declaration under `billing_components`, keyed by endpoint path. The OpenAPI document does not change.

The SDK checks each declaration at deploy time. A declaration must have exactly the keys `version`, `schema_id`, and `app`.
`version` must be `1`, `schema_id` must be a non-empty string, and `app` must be an object schema.
A declaration must contain only JSON values, with no tuples, sets, or NaN.
WebSocket endpoints cannot declare billing components. The `/start-session` endpoint does not declare them.

## Relay a partner media server

Some apps do not process media on the fal runner. In this mode, the browser connects directly to your own media server.
The fal app relays signaling and control updates over a [`@fal.realtime`](realtime.md) WebSocket.
fal still handles authentication and billing.
This pattern uses a standard `fal.App` with a realtime endpoint, not `fal.wma.App`.

`fal.wma.WebSocketSignalingRelay` provides the session logic for this pattern:

* It connects to your media server's signaling WebSocket with a timeout and reports a clear error if the connection fails.
* It sends bootstrap messages, such as ICE servers, to the client before signaling starts.
* It forwards SDP offers and ICE candidates at once, so slow control updates cannot block them.
* It holds control updates until your media server sends its answer, then delivers them in order.
* It closes both sides when either side disconnects.

You supply hooks that translate between your client messages and your media server's protocol:

| Hook | Purpose |
| - | - |
| `signaling_payload` | Converts a client offer or ICE candidate into an upstream message. |
| `control_payload` | Converts a client control update. Raise `fal.wma.ControlRejected` to refuse it. |
| `from_upstream` | Converts an upstream message into a client output. Return `None` to drop it. |
| `is_signaling` | Returns `True` when a client message carries signaling. |
| `has_control` | Returns `True` when a client message carries a control update. |
| `is_session_ready` | Returns `True` for the output that completes negotiation, usually the answer. |
| `make_error` | Builds the error output that the client receives. |

A rejected control update sends an error to the client and keeps the session open.
More than `max_pending_controls` queued updates (default 64) ends the session.
The default upstream connection uses the `websockets` package. Add it to your app's `requirements`.

The following outline shows where the relay fits. It does not run until you define the hook functions it names:

```python theme={null}
import fal
import fal.wma
from fal.wma.duration_billing import install_duration_billing, mark_billing_started


class PartnerApp(fal.App):
    requirements = ["websockets"]

    @fal.realtime("/realtime")
    async def realtime(self, inputs):
        # Define each hook function before you deploy this app.
        relay = fal.wma.WebSocketSignalingRelay(
            upstream_url="wss://media.example.com/signal",
            signaling_payload=signaling_payload,
            control_payload=control_payload,
            from_upstream=from_upstream,
            is_signaling=is_signaling,
            has_control=has_control,
            is_session_ready=is_session_ready,
            make_error=make_error,
        )
        async for output in relay.run(inputs):
            # Use the same test as your is_session_ready hook.
            if output.type == "answer":
                mark_billing_started()
            yield output


install_duration_billing(PartnerApp, "realtime", label="partner-app")
```

### Bill relay sessions by duration

A `@fal.realtime` handler cannot set billable units on a response.
`install_duration_billing` wraps the handler so that the session reports its duration in seconds.
Ask the fal team to price the endpoint per second before you rely on duration billing.

* Call `install_duration_billing` once, after the class body. Its `label` names the app in billing log lines.
* Call `mark_billing_started()` when your media server answers. Connection and signaling time are free. If you never call it, the session reports zero time.
* Call `mark_session_failed()` after a failure. It stops further billing. Time already reported stays billed, and time not yet reported is not billed.

By default, sessions that end within 2 seconds of `mark_billing_started()` bill zero.
Longer sessions bill the full time from that call, including the first 2 seconds.

By default, the session reports its time every 5 seconds. If a client disconnects without a close, up to one interval can go unbilled.

Set `grace_period` and `update_interval` on `install_duration_billing` to change these values.
`grace_period` must be zero or more, and `update_interval` must be more than zero.
Set `grace_period` to `0` to turn off the grace period.

`minimum_billable_units` does not apply to these sessions.

## Troubleshooting

| Symptom | Check |
| - | - |
| `No module named fal.wma` | Install the required SDK version in the same environment that runs `fal deploy`. |
| `aiortc` is missing locally | Install the `fal[wma]` extra. |
| The playground asks for SDP | Declare `realtime_contract`, redeploy, and refresh. |
| Schema generation fails | Use Pydantic 2 locally and in the app's runner requirements. |
| A private app returns 401 or 403 | Check account access, credentials, and the browser's authenticated server proxy. |
| Controls work but no video arrives | Declare the correct client receive tracks and attach an outgoing server track. |
| Connections fail on restrictive networks | Check TURN provisioning and the selected ICE path. |
| A session closes after two minutes | The three media examples intentionally impose this limit. |

See the [SDK guide](https://github.com/fal-ai/fal/blob/fal_v1.82.1/projects/fal/WMA.md) for module details and custom backend responsibilities.
