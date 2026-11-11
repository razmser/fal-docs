> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../documentation/quickstart.md) and the [Model APIs overview](../../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Deploy Matrix-Game 2.0 Real-time World Model with WMA

> Deploy Matrix-Game 2.0 as a real-time, keyboard-controlled world model on one H100. Stream interactive video over WebRTC with the fal WMA SDK.

This example deploys [Matrix-Game 2.0](https://huggingface.co/Skywork/Matrix-Game-2.0), a real-time world model from Skywork (MIT license, 1.8B parameters), on one H100.
The model starts from a seed image and generates video that reacts to movement and camera keys.
The app uses [fal WMA (World Model Accelerator)](../../documentation/development/wma.md) (`fal.wma`) to stream the video over WebRTC.

> [!TIP]
>
> Want a hosted world model instead of deploying your own? Try [Lingbot World 2](https://fal.ai/models/robbyant/lingbot-world-2) or [Abot World 0](https://fal.ai/models/alibaba/abot-world-0) on fal. Both let you explore an interactive world in real time with movement and camera controls, with nothing to deploy. Use this example when you want to run your own world model.

<video autoPlay loop muted playsInline controls>
  <source src="https://mintcdn.com/fal-d8505a2e/e51eBJ8MO88LLecM/images/examples/wma-matrix-game.mp4?fit=max&auto=format&n=e51eBJ8MO88LLecM&q=85&s=85e08acf51fc5faa937e62a2cce149a2" type="video/mp4" />
</video>

The clip above is a recording of this app on one H100. The caption shows each `commands` message as the client sent it.

The example shows the `CommandWorld` interaction profile:

* The world starts when the session starts. The first video frame tells the client that the world is ready.
* The client sends the commands that are held and the commands that were tapped.
* An optional `setup` message chooses the seed image. A `reset` message restarts the world.

> [!WARNING]
>
> WMA is experimental. This page pins `fal` 1.82.1. Read the [WMA guide](../../documentation/development/wma.md) before you deploy.

## What Is a World Model

A world model generates video that responds to what you do. A video model turns a prompt into a clip of fixed length. A world model produces the next frames one chunk at a time, and each chunk depends on the frames before it and on the latest actions from the user, such as movement and camera keys. Because the user's input shapes every new frame, the output works like a game: you can walk around, look around, and see the world change in response.

That loop has two consequences for how you serve it:

* **Frames must arrive in real time.** The app streams the generated frames to the client as a video track, not as a finished file. This is why the app streams over WebRTC.
* **Each session holds state.** The model keeps the world it has already generated, so a session belongs to one user and runs on one GPU for as long as it lasts.

[WMA](../../documentation/development/wma.md) handles the streaming, the control messages, and the session lifecycle, so the app only has to implement the generation loop.

## Key Facts

| Item | Value |
| - | - |
| Model | Matrix-Game 2.0 from Skywork, 1.8B parameters, MIT license |
| GPU | One H100, about 14 GB of GPU memory |
| Weights | About 12 GB, downloaded on the first start of a runner |
| Video | 640x352 in our tests, played at 25 frames per second |
| Generation speed | About 19 to 23 frames per second in our tests, slightly slower than playback |
| First frame | 1 to 4 seconds after the session connects, in our tests |
| First start | 5 to 20 minutes in our tests |
| World length | At most 1437 frames, about 57 seconds |
| Sessions | One session for each runner |

## Prerequisites

* A fal account with permission to deploy and call the app. The browser client connects through fal's WMA service at `wma.fal.run`. It needs a fal API key on your server, or a signed-in dashboard session for the playground.
* Access to the fal WMA TURN servers for the account that owns the app (the `X` in `fal.run/X/app`). Request it from fal before you rely on the app. Without it, a session connects only when the runner is directly reachable, which fails on many runners and networks. See [If the Session Does Not Start](#if-the-session-does-not-start).
* Python 3.12 on your computer. The app image uses Python 3.12, and the Python versions must match for deployment.
* `fal` 1.82.1 and authentication. Run these commands:

```bash theme={null}
git clone https://github.com/fal-ai-community/fal-demos.git
cd fal-demos
uv venv --python 3.12
source .venv/bin/activate
uv pip install 'fal[wma]==1.82.1'
fal auth login
```

* An H100 [machine type](../../documentation/deployment/machine-types.md). On one H100 the app uses about 14 GB of GPU memory.

The app downloads two pinned sources on the first start of a runner and stores them in `/data`:

* The upstream code from [SkyworkAI/Matrix-Game](https://github.com/SkyworkAI/Matrix-Game) at commit `c7a88bf`. Only the `Matrix-Game-2` folder is checked out.
* The weights from `Skywork/Matrix-Game-2.0` at revision `08c42d1`, about 12 GB for the files that the app needs.

## The App

View the complete source code on [GitHub](https://github.com/fal-ai-community/fal-demos/blob/main/fal_demos/video/wma/matrix_game.py).
This page shows the parts that use the WMA SDK.

### The Contract

The contract declares one video track and the `CommandWorld` profile.
The `Commands` message holds the eight semantic commands.
The contract describes the app to the playground and to clients. The handlers do the real work.
The values `"/active"` and `"/activated"` are JSON Pointers into the `commands` message.

```python theme={null}
CONTRACT = fal.wma.RealtimeContract(
    media=fal.wma.MediaContract(
        receive=(fal.wma.Track(kind="video", width=WIDTH, height=HEIGHT),)
    ),
    client_messages=ClientMessage,
    server_messages=ServerMessage,
    interaction=CommandWorld(
        commands=CommandState(Commands, "/active", "/activated"),
        setup=SerialAction(Setup, Configured),
        reset=SerialAction(Reset, ResetApplied),
        errors=CommandErrors(fal.wma.ErrorMessage, "/error"),
        ended=fal.wma.StreamExhaustedMessage,
    ),
    client_message_examples=(
        fal.wma.MessageExample(
            name="Walk forward", payload={"type": "commands", "active": ["forward"]}
        ),
        fal.wma.MessageExample(
            name="Turn right while walking",
            payload={"type": "commands", "active": ["forward", "look_right"]},
        ),
        fal.wma.MessageExample(
            name="Stop", payload={"type": "commands", "active": []}
        ),
        fal.wma.MessageExample(name="Restart", payload={"type": "reset"}),
    ),
)
```

### The Control Handlers

The app registers one handler for each message type. The handlers run on the session event loop.

```python theme={null}
    def on_commands(self, message: Dict[str, Any]) -> None:
        try:
            parsed = Commands.model_validate(message)
        except ValueError as exc:
            self.session.send({"type": "error", "error": str(exc)[:200]})
            return
        self.keys.update(list(parsed.active), list(parsed.activated))
# ...
    def request_restart(self, scene: Optional[int], ack: type) -> None:
        with self.lock:
            self.restart = (scene, ack)
        # Drop frames of the old world that wait for playback.
        while not self.frames.empty():
            self.frames.get_nowait()
            self.flushed += 1
```

### Billing in the Generation Loop

The generation thread bills each frame after it queues the frame. If billing is already final, the session is closing and the thread stops.

```python theme={null}
                            if not self.push(frame):
                                return
                            try:
                                self.session.add_billable_units(1)
                            except RuntimeError:
                                return  # Billing is final, so the session is closing.
```

### The App Class

The class sets the runner, the pinned requirements, and a long `startup_timeout` for the weight download.
The `keep_alive` value keeps a runner up for 300 seconds after its last session, and fal bills that idle time. The value `min_concurrency=0` scales the app to zero, so the first session waits for a cold start. The `max_concurrency` value is the most runners, and `max_multiplexing` is the most sessions on one runner. Both are example limits. Adjust them for your workload and cost. See [Scale your application](../../documentation/deployment/scale-your-application.md).

```python theme={null}
class MatrixGameApp(
    fal.wma.App,
    python_version="3.12",
    keep_alive=300,
    min_concurrency=0,
    max_concurrency=2,
    max_multiplexing=1,
):
    machine_type = "GPU-H100"
    startup_timeout = 1800  # Loading about 12 GB of weights can take many minutes.
# ...
        "pydantic>=2,<3",
    ]
    realtime_contract = CONTRACT
```

### The Session Backend

The `create_backend` method accepts one session at a time. It then starts the generation thread and the WebRTC peer.
The lines that build the ICE servers (the `RunnerIceConfig` call) are boilerplate. Copy them as they are.

```python theme={null}
    async def create_backend(self, session):
        from aiortc import RTCConfiguration

        # One session at a time: the KV caches belong to the runner, not the session.
        if not self.slot.try_acquire():
            raise fal.wma.AppError(
    # ...
        session.defer(self.slot.release)
        world = WorldSession(self.engine, session)
        # Deferred cleanups run in reverse: join the thread, cap billing, release the slot.
        session.defer(world.cap_billing)
        session.defer(world.stop_and_join)

        servers, _ = await fal.wma.RunnerIceConfig.from_bridge().build_ice_servers_async(
            session.offer.ice_servers, forwarded_status=session.offer.ice_status
        )
        track = fal.wma.make_video_queue_track(world.frames, FPS)
        world.thread.start()
        return fal.wma.AiortcPeer(
            session,
            lambda pc: pc.addTrack(track),
            rtc_configuration=RTCConfiguration(iceServers=servers),
            disconnected_grace_seconds=10,
        )
```

The module imports on a computer without a GPU. The app imports `torch` and the upstream code inside `Engine.load()`.

## How the Contract Maps to Controls

The app declares eight semantic commands. The client does not send physical key names.
The browser, a gamepad, or an agent can map any input to these commands.

| Command | Matrix-Game conditioning |
| - | - |
| `forward`, `back`, `left`, `right` | Keyboard vector `[W, S, A, D]` |
| `look_up`, `look_down` | Mouse pitch, plus or minus 0.1 |
| `look_left`, `look_right` | Mouse yaw, minus or plus 0.1 |

Each `commands` message reports the complete held state in `active`. It reports newly tapped commands in `activated`.
`fal.wma.KeyState` merges both lists. It keeps a tap for one generation step and a held command until the client releases it.
The generation thread calls `KeyState.sample()` once for each chunk.
A new tap wins over an older held command in the same conflict group. If the client holds both commands of a group, `KeyState` keeps both, and the app cancels them.

The model works in chunks. One chunk is three latent frames, which decode to 12 video frames. The first chunk decodes to 9 frames.
All frames of a chunk use the same keyboard and mouse values.
This is how the upstream `inference_streaming.py` script conditions the model. The app replaces its terminal prompt with the sampled state.

The other messages follow the `CommandWorld` profile:

| Message | Direction | Effect |
| - | - | - |
| `setup` with `scene` from 0 to 16 | Client to app | Restarts the world from one of the 17 upstream demo images. The app accepts it once. It replies with `configured`. |
| `reset` with an optional `scene` | Client to app | Restarts the world. It replies with `reset_applied`. |
| `error` | App to client | Reports a message that the app rejected, or a generation failure. |
| `stream_exhausted` | App to client | The world reached its length limit and the session closes. |

## Run and Deploy

Run these commands from the root of the cloned repository.
Use `fal run` as the development loop. It starts the app on an ephemeral runner.

```bash theme={null}
fal run fal_demos/video/wma/matrix_game.py::MatrixGameApp
```

Deploy the app as a private app. Use the deployed app for the playground and for the browser client in the next section.

```bash theme={null}
fal deploy fal_demos/video/wma/matrix_game.py::MatrixGameApp --auth private
```

### Cost and First Start

You pay per second for the runner, including setup time and the idle `keep_alive` time. See [Pricing](../../documentation/serverless/pricing.md). The billable units in the app matter only if you [list the app on the fal Marketplace](../../documentation/serverless/publishing-to-marketplace.md). Callers then pay for these units at a price that the fal team sets.

The first session starts a runner, and that start is slow. The runner installs the requirements, then downloads and loads about 12 GB of weights. In our tests, this took 5 to 20 minutes, so the app sets `startup_timeout` to 1800 seconds. You can follow the progress in the runner logs in the dashboard.

Later starts are faster because the weights stay on the [`/data` volume](../../documentation/development/use-persistent-storage.md) across runners. To skip the cold start, set `min_concurrency = 1` to keep one runner warm. You then pay for that runner while it is idle. See [cold starts](../../documentation/serverless/optimizations/cold-start-scaling.md).

### Clean Up

To stop paying for the app, scale it to zero with `fal apps scale <app-name> --min-concurrency 0`. To remove it, run `fal apps delete <app-name>`.

## Try It

Open the deployed app in the fal dashboard. In the **Playground** tab, select the `/start-session` endpoint.

1. Select **Connect** to start a session. The first frame shows that the world is ready.
2. Hold a movement or look control, or its keyboard key, to move. Release it to stop.
3. Use the **Restart world** control to reset the world.

### Browser Client

For your own page, connect with the experimental [WMA browser client](../../documentation/development/wma-browser.md). There is no Python or Node client today.
The deploy output shows the app ID as `your-account/<app-name>`. The name comes from the class name, so `MatrixGameApp` becomes `matrix-game-app`.
Install the client and the server proxy in your web application. Set up the proxy route as the browser client page describes.

```bash theme={null}
npm install @fal-ai/client@1.11.0-alpha.5 @fal-ai/server-proxy@1.2.1
```

The client sends the WMA signaling requests through your proxy route. The proxy adds your API key on the server, so the key never reaches the browser. The video then flows over WebRTC, not through your proxy.
The following code sends the held and tapped keys of the keyboard. Replace the application ID with your own.

```typescript theme={null}
import { fal } from "@fal-ai/client";
import { wma } from "@fal-ai/client/realtime/wma";

fal.config({ proxyUrl: "/api/fal/proxy" });

const keyMap: Record<string, string> = {
  w: "forward", s: "back", a: "left", d: "right",
  i: "look_up", k: "look_down", j: "look_left", l: "look_right",
};
const held = new Set<string>();
let controlsReady = false;
// The element needs the muted and playsinline attributes for autoplay: <video id="output" muted playsinline>
const video = document.querySelector<HTMLVideoElement>("#output")!;
// The world is ready when the first frame plays.
video.addEventListener("playing", () => {
  controlsReady = true;
});

let ended = false;

function endSession(message: string) {
  // A failure reaches both onError and onState, so run the cleanup once.
  if (ended) return;
  ended = true;
  controlsReady = false;
  held.clear();
  video.srcObject = null;
  console.log(message);
}

const session = fal.realtime.open(wma("your-account/<app-name>"), {
  receive: ["video"],
  onMedia(stream) {
    video.srcObject = stream;
    void video.play().catch(console.error);
  },
  onData: (raw) => {
    const msg = JSON.parse(raw);
    switch (msg.type) {
      case "reset_applied":
        console.log("World restarted from scene", msg.scene);
        break;
      case "error":
        console.error(msg.error);
        break;
      case "stream_exhausted":
        endSession("The world reached its length limit.");
        break;
    }
  },
  onState(state) {
    if (state === "closed" || state === "failed") endSession("Session ended.");
  },
  onError(error) {
    console.error(error);
    endSession("Session failed.");
  },
});
try {
  await session.ready;
} catch (error) {
  endSession("Session failed to start.");
  await session.close();
  throw error;
}

function report(tapped: string[] = []) {
  if (ended) return;
  session.send({ type: "commands", active: [...held], activated: tapped });
}

window.addEventListener("keydown", (event) => {
  const command = keyMap[event.key.toLowerCase()];
  if (!command || event.repeat || !controlsReady) return;
  held.add(command);
  report([command]);
});
window.addEventListener("keyup", (event) => {
  const command = keyMap[event.key.toLowerCase()];
  if (!command) return;
  held.delete(command);
  report();
});
window.addEventListener("blur", () => {
  held.clear();
  report();
});
window.addEventListener("pagehide", () => void session.close());
```

The `onData` callback receives the raw JSON string. Parse it before you use it.
Enable the controls after the video element fires `playing`. The `blur` handler releases all held keys when the tab loses focus.
A session can end on its own. The `stream_exhausted` message closes the session when the world reaches its length limit. In that case, open a new session. To reconnect, wrap `fal.realtime.open(...)` and the `ended` flag in a function.
The `try` and `catch` around `await session.ready` handle a failed start.
A failure reaches both `onError` and `onState`. The `ended` flag makes `endSession` run once.

### Server Messages

| Message | Example |
| - | - |
| `configured` | `{"type": "configured", "scene": 0}` |
| `reset_applied` | `{"type": "reset_applied", "scene": 0}` |
| `error` | `{"type": "error", "error": "generation failed"}` |
| `stream_exhausted` | `{"type": "stream_exhausted"}` |

## If the Session Does Not Start

| Symptom | Cause and action |
| - | - |
| `401` with a body such as `{"error": "missing Authorization header"}` or `{"error": "account is locked"}` | The request has no valid credential, or the account is locked. Check the proxy and your fal API key. |
| `session.ready` rejects with "Another session is active on this runner. Retry." | The runner already serves a session, so the app rejects the new one with HTTP 503 and the error type `session_busy`. Retry after a short delay. |
| The connection never leaves `checking`, or fails on a restrictive network | The app owner does not have access to the fal WMA TURN servers. Request access from fal. Without it, the app uses a direct connection with STUN only. |

## Use Your Own Model

The model is the `Engine` class. It has three parts.

* `load()` loads the weights once for each runner.
* `new_world(scene)` starts a world from a seed image.
* `step(world, keys)` returns a chunk of `uint8` frames with the shape `[T, H, W, 3]`.

The rest is the same WMA shape for any model:

* A generation thread calls `step` in a loop. It reads the sampled keys and checks for a restart before each chunk.
* The thread puts each frame in a bounded queue, with the `push` method. The method blocks while the queue is full.
* `make_video_queue_track` plays the queue as the video track, and `AiortcPeer` sends it to the client.

```python theme={null}
    def push(self, frame: Any) -> bool:
        """Queue a frame. Block while the queue is full. False when the session stops."""
        future = asyncio.run_coroutine_threadsafe(self.frames.put(frame), self.loop)
        while not self.stop.is_set():
            try:
                future.result(timeout=0.2)
                return True
            except concurrent.futures.TimeoutError:
                continue
        future.cancel()
        return False
```

## Billing

This section applies if you [list the app on the fal Marketplace](../../documentation/serverless/publishing-to-marketplace.md). For your own private app, you pay only for runner time, and the units do not change the cost.

The app calls `session.add_billable_units(1)` for each frame that it queues for playback. One unit is one frame.
Some frames never play. The app drops the queued frames when a `reset` or `setup` message restarts the world, and some frames remain queued when the session ends.
Before billing is final, a deferred cleanup calls `session.cap_billable_units(total - undelivered)`. The argument is the new total, so the undelivered frames are not billed.
The [WMA guide](../../documentation/development/wma.md#ice-billing-and-deployment) describes this billing pattern. fal reports the total when the session closes.
The fal team configures the price per unit when it lists the app.

## Limits

* Each runner serves one session at a time. With `max_concurrency = 2`, the example serves at most two sessions at once.
* A world lasts at most 1437 video frames, about 57 seconds at playback speed. The app then sends `stream_exhausted` and closes the session. Use `reset` to start again before that point.
* The app plays video at 25 frames per second, the rate that upstream states for real-time generation. In our tests on one H100, generation ran at about 19 to 23 fps at 640x352, which is slower than playback, so motion can look bursty. The first frame arrived 1 to 4 seconds after we connected.
* A key change takes effect at the next chunk. The delay is the time to generate a chunk plus the frames that wait in the queue.
* Upstream notes that an upward camera movement can cause brief rendering glitches, such as black frames.
* The example supports the `universal` model only. The upstream `templerun` model uses different weights and a different command set.
