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

# Deploy StreamDiffusionV2 Video Style Transfer with WMA

> Deploy StreamDiffusionV2 for real-time video-to-video style transfer on one H100. Restyle a live webcam stream with a text prompt, using the fal WMA SDK.

This example deploys real-time video style transfer on one H100 with the [WMA SDK](../../documentation/development/wma.md).
It serves [StreamDiffusionV2](https://github.com/chenfengxu714/StreamDiffusionV2), a streaming video-to-video diffusion pipeline built on Wan2.1 1.3B.
The browser sends its camera. The app restyles each frame with a text prompt and sends the result back as a live video track.

> [!TIP]
>
> Need to restyle a finished video and not a live stream? Use a hosted video editing API such as [Kling O3 Edit Video](https://fal.ai/models/fal-ai/kling-video/o3/pro/video-to-video/edit), which changes an existing clip with a text prompt. Use this example when you want to restyle live camera input in real time with your own model.

<video autoPlay loop muted playsInline controls>
  <source src="https://mintcdn.com/fal-d8505a2e/e51eBJ8MO88LLecM/images/examples/wma-video-restyle.mp4?fit=max&auto=format&n=e51eBJ8MO88LLecM&q=85&s=d7d5eb4dbd9c85e242703ec56a17a499" type="video/mp4" />
</video>

The clip above is a recording of this app on one H100, with a sample video in place of the camera. The inset shows the camera input, and the caption shows the `prompt` message that the client sent.

> [!WARNING]
>
> The [WMA SDK](../../documentation/development/wma.md) is experimental. Pin the SDK version, as this page does.

## Key Facts

| Item | Value |
| - | - |
| Model | StreamDiffusionV2, built on Wan2.1 1.3B |
| GPU | One H100 |
| Video | 512 by 512 at 16 frames per second |
| Frame gap | About 63 ms median, 110 to 130 ms at the 95th percentile, across two test runs |
| Style change | Output pauses for under half a second in our tests |
| First start | About 7 minutes on a fresh runner, which downloads about 23 GB of weights |
| Session limit | 300 seconds |

## What the App Does

The client sends camera video, the app restyles it, and a new video track comes back.
The [camera effects example](../../documentation/development/wma.md#choose-an-example) shows the same pattern with a CPU filter. Its source is [`wma_filter.py`](https://github.com/fal-ai/fal/blob/fal_v1.82.1/projects/fal/examples/wma_filter.py) (`FilterApp`).

* Each camera frame is cropped to a square, and the camera is resampled to 16 frames per second.
* The model takes frames in chunks of 4. Each chunk produces 4 restyled frames.
* A `prompt` message from the client changes the style while the stream runs.
* A `status` message goes to the client when the state changes, and every 2 seconds.

The client can change the style without a new session, but the change is not instant.
The upstream pipeline reads the prompt only when a stream starts, so the app starts a new stream from the newest camera frames.
Output pauses briefly, and the `status` message reports `restarting` during the pause.
When several prompts arrive quickly, only the latest counts.

## Prerequisites

* A fal account with permission to deploy and call the app. The browser client connects through fal's WMA service at `wma.fal.run`. It needs a fal API key on your server, or a signed-in dashboard session for the playground.
* Access to the fal WMA TURN servers for the account that owns the app (the `X` in `fal.run/X/app`). Request it from fal before you rely on the app. Without it, a session connects only when the runner is directly reachable, which fails on many runners and networks. See [If the Session Does Not Start](#if-the-session-does-not-start).
* Python 3.11 on your computer. The app image uses Python 3.11, and the Python versions must match for deployment.
* The fal CLI and the demo code. Run these commands:

```bash theme={null}
git clone https://github.com/fal-ai-community/fal-demos.git
cd fal-demos
uv venv --python 3.11
source .venv/bin/activate
uv pip install 'fal[wma]==1.82.1'
fal auth login
```

* A browser with a camera. Camera capture needs HTTPS or localhost.
* One H100 GPU. The app uses the upstream single-GPU path with 2 denoising steps. It does not use the multi-GPU pipeline.

The weights are public and not gated. Both repositories use the Apache-2.0 license.
The first runner downloads about 23 GB to `/data`, and later runners reuse the files.
The app downloads the upstream code as an archive of one pinned commit, so the runner does not need `git`.

## The App

View the complete source code on [GitHub](https://github.com/fal-ai-community/fal-demos/blob/main/fal_demos/video/wma/video_restyle.py).
This page shows the parts that use the WMA SDK.

### Messages

The client sends one `prompt` message. The app sends `status` and `rejected` messages.

```python theme={null}
class Prompt(BaseModel):
    type: Literal["prompt"] = "prompt"
    text: str = Field(
        min_length=1,
        max_length=400,
        description="Style prompt. The stream restarts to apply it.",
    )
# ...
ServerMessage = Annotated[Union[Status, Rejected], Field(discriminator="type")]
```

### The App Class

The class sets the runner, the pinned requirements, and a long `startup_timeout` for the weight download.
The `max_multiplexing` value of 1 gives each session the whole GPU.

```python theme={null}
class RestyleApp(fal.wma.App, python_version="3.11"):
    machine_type = "GPU-H100"
    requirements = [
        "fal[wma]==1.82.1",
# ...
        "aiortc",
    ]
    startup_timeout = 1800  # Weights download and kernel compilation take minutes.
    min_concurrency = 0
    max_concurrency = 2
    max_multiplexing = 1  # One session owns the GPU and the model state.
    keep_alive = 300
```

The `max_concurrency` and `max_multiplexing` values are example limits. Adjust them for your workload and cost.

### The Contract

The contract declares a required camera track, a square output track, and example prompts.

```python theme={null}
    realtime_contract = fal.wma.RealtimeContract(
        media=fal.wma.MediaContract(
            send=(
                fal.wma.Track(
                    kind="video",
                    source="camera",
                    required=True,
                    width=fal.wma.Constraint(ideal=640),
                    height=fal.wma.Constraint(ideal=480),
                    frame_rate=fal.wma.Constraint(ideal=30),
                ),
            ),
            receive=(
                fal.wma.Track(kind="video", width=SIZE, height=SIZE, frame_rate=FPS),
            ),
        ),
        client_messages=Prompt,
        server_messages=ServerMessage,
```

### The Prompt Handler

The handler validates the text and stores it. The model thread restarts the stream when the prompt changes.

```python theme={null}
    def on_prompt(self, message):
        try:
            prompt = Prompt.model_validate(message)
        except ValueError:
            self.session.send(
                Rejected(reason="text must be 1 to 400 characters").model_dump()
            )
            return
        with self.cond:
            self.prompt = prompt.text
            self.cond.notify_all()
```

### The Session Backend

The `create_backend` method attaches the output track when the camera track arrives.
It also ends the session after `MAX_SESSION_SECONDS`.
The lines that build the ICE servers (the `RunnerIceConfig` call) are boilerplate. Copy them as they are.

```python theme={null}
        def connect(pc):
            attached = False

            @pc.on("track")
            def incoming(track):
                nonlocal attached
                if track.kind == "video" and not attached:
                    attached = True
                    pc.addTrack(
                        fal.wma.make_video_queue_track(
                            restyle.out, fps=FPS, rebase_after_stall=True
                        )
                    )
                    restyle.start(track)

        async def expire():
            await asyncio.sleep(MAX_SESSION_SECONDS)
            await session.close()

        session.create_task(expire())
        return fal.wma.AiortcPeer(
            session,
            connect,
            rtc_configuration=RTCConfiguration(iceServers=servers),
            disconnected_grace_seconds=10,
        )
```

## How the App Works

### Pinned Upstream Code

The upstream repository is not a pip package at this commit, so the app downloads the source archive and adds it to `sys.path`.
The commit and both weight revisions are constants at the top of the file.
The upstream code reads the `STREAMDIFFUSIONV2_ROOT` environment variable to find `wan_models/` and `ckpts/`.
The app sets this variable before it imports the pipeline. All heavy imports run inside `prepare_runtime`, so the file imports on a computer without PyTorch.

The app calls the upstream `SingleGPUInferencePipeline` directly, as the upstream web demo does.
The `StreamDiffusionV2Pipeline` class in the README reads a complete video file, so it does not fit live input.
`setup()` also runs a short warm-up stream, so the first session does not pay for kernel compilation.

### Frame Buffering and Dropping

Latency stays bounded because every buffer in the app has a fixed size.

1. `read_camera` reads every incoming frame and keeps only the newest one, so the camera track queue never grows.
2. `sample_camera` copies the newest frame into an input buffer 16 times each second. This converts any camera rate to the model rate.
3. The input buffer holds at most 8 frames. If the model falls behind, the app drops the oldest frames and counts them in `dropped_input_frames`.
4. The model thread takes 4 frames for each step. A new stream takes the 5 newest frames, because the upstream pipeline needs `1 + 4` frames to start.
5. The output queue holds at most 16 frames. The app drops the oldest frame when the queue is full, with `queue_put_drop_oldest`, and counts it as undelivered.

`make_video_queue_track` paces the output at 16 frames per second.
With `rebase_after_stall=True`, the track rebases its timestamps after a stall, such as a prompt restart. This prevents a burst of late frames.

### Model Thread

The model call blocks, so it runs on its own thread and never on the event loop.
The thread calls `loop.call_soon_threadsafe` to give frames to the output queue.
The `shutdown` function is a deferred cleanup. It stops the thread and waits for the thread to end before the session closes.
The `cap_billing` function runs after `shutdown` and before billing is final.
The app sets `max_multiplexing = 1` because the model keeps one stream state, and a lock protects it.

## Contract and Client Controls

The `realtime_contract` describes the session for the playground and for client code.

| Contract field | Value | Client control |
| - | - | - |
| `media.send` | Required camera video track | Pass your camera stream as `localStream`. |
| `media.receive` | One 512 by 512 video track at 16 frames per second | Pass `receive: ["video"]`. |
| `client_messages` | `prompt` with a `text` field | Send `{ type: "prompt", text: "..." }`. |
| `server_messages` | `status` and `rejected` | Read the messages in `onData`. |

The app does not use an interaction profile, because it has one optional text control and a default prompt.
To track each prompt through events, add `ConfiguredSession`, as the [WMA reference](../../documentation/development/wma.md#declare-client-interactions) describes.

## Run and Deploy

From the root of the cloned repository, use `fal run` as the development loop. It starts the app on an ephemeral runner:

```bash theme={null}
fal run fal_demos/video/wma/video_restyle.py::RestyleApp
```

To deploy the app as a private app, run the next command. Use the deployed app for the playground and the browser client below.

```bash theme={null}
fal deploy fal_demos/video/wma/video_restyle.py::RestyleApp --auth private
```

### Cost and First Start

You pay per second for the runner, including setup time and the `keep_alive` time (the idle seconds a runner stays up after its last session). See [Pricing](../../documentation/serverless/pricing.md). The billable units in the app matter only if you [list the app on the fal Marketplace](../../documentation/serverless/publishing-to-marketplace.md). Callers then pay for these units at a price that the fal team sets.

The first session starts a runner. The runner downloads about 23 GB of weights and compiles the model. On a fresh runner this took about 7 minutes in our tests.
Watch the runner logs in the dashboard. The `/data` volume keeps the weights across runners. See [persistent storage](../../documentation/development/use-persistent-storage.md).
The default `min_concurrency = 0` scales the app to zero, so the first session waits for a cold start. To keep a runner warm, set `min_concurrency = 1`. A warm runner costs idle time. See [cold starts](../../documentation/serverless/optimizations/cold-start-scaling.md).

### Clean Up

To stop paying for the app, scale it to zero with `fal apps scale <app-name> --min-concurrency 0`. To remove it, run `fal apps delete <app-name>`.

The idle runner timeout (`keep_alive`) is 300 seconds. A runner stays up for that time after its last session.
The maximum session length is also 300 seconds (`MAX_SESSION_SECONDS`). Change the constant to change the limit.

## Try It

### Playground

1. Open the deployed app in the fal dashboard.
2. Select its `/start-session` playground.
3. Select **Connect** and allow the camera.
4. Wait for the restyled video.
5. Send a prompt example, such as **Claymation**.
6. Read the `status` messages in the message log. The `state` field reports `restarting` while the app applies the new prompt.
7. Select **Disconnect** to end the session.

### Browser Client

The experimental [WMA browser client](../../documentation/development/wma-browser.md) sends the camera and the prompt. There is no Python or Node client today.
The deploy output shows the app ID as `your-account/<app-name>`. The name comes from the class name, so `RestyleApp` becomes `restyle-app`.
Install the client and the server proxy in your web application. Set up the proxy route as the browser client page describes.

```bash theme={null}
npm install @fal-ai/client@1.11.0-alpha.5 @fal-ai/server-proxy@1.2.1
```

The API key stays on your server behind the proxy route.
This function uses only the options that the browser guide documents.

```typescript theme={null}
import { fal } from "@fal-ai/client";
import { wma } from "@fal-ai/client/realtime/wma";

fal.config({ proxyUrl: "/api/fal/proxy" });

export async function connectRestyle(
  appId: string,
  output: HTMLVideoElement,
  onEnded: (reason: string) => void,
): Promise<{ setPrompt(text: string): void; close(): Promise<void> }> {
  const camera = await navigator.mediaDevices.getUserMedia({
    video: true,
    audio: false,
  });
  const stopCamera = () => camera.getTracks().forEach((track) => track.stop());
  let ended = false;
  const finish = (reason: string) => {
    // A failure reaches both onError and onState, so run the cleanup once.
    if (ended) return;
    ended = true;
    stopCamera();
    output.srcObject = null;
    onEnded(reason);
  };
  const session = fal.realtime.open(wma(appId), {
    localStream: camera,
    receive: ["video"],
    onMedia(stream) {
      output.srcObject = stream;
      void output.play().catch(console.error);
    },
    onData: (raw) => {
      const msg = JSON.parse(raw);
      switch (msg.type) {
        case "status":
          console.log(msg.state, msg.output_frames, msg.dropped_input_frames);
          break;
        case "rejected":
          console.error(msg.reason);
          break;
      }
    },
    onState(state) {
      if (state === "closed" || state === "failed") finish(state);
    },
    onError(error) {
      console.error(error);
      finish("failed");
    },
  });
  try {
    await session.ready;
  } catch (error) {
    stopCamera();
    await session.close();
    throw error;
  }
  return {
    setPrompt: (text) => session.send({ type: "prompt", text }),
    async close() {
      try {
        await session.close();
      } finally {
        stopCamera();
        output.srcObject = null;
      }
    },
  };
}
```

The `onData` callback receives the raw JSON string. Parse it before you use it.
On a closed or failed session, the code stops the camera and calls `onEnded`. Use that call to show that the session ended.
A failure reaches both `onError` and `onState`. The `ended` flag makes the `finish` function run once.
A session can end on its own. The app closes each session after 300 seconds, and after a generation failure it sends a `rejected` message with the reason "generation failed" and closes the session.

### Server Messages

| Message | Example |
| - | - |
| `status` | `{"type": "status", "state": "running", "prompt": "A claymation character", "output_frames": 128, "dropped_input_frames": 0}` |
| `rejected` | `{"type": "rejected", "reason": "text must be 1 to 400 characters"}` |

The `state` field of `status` is `waiting_for_camera`, `running`, or `restarting`.

## If the Session Does Not Start

| Symptom | Cause and action |
| - | - |
| `401` with a body such as `{"error": "missing Authorization header"}` or `{"error": "account is locked"}` | The request has no valid credential, or the account is locked. Check the proxy and your fal API key. |
| The connection never leaves `checking`, or fails on a restrictive network | The app owner does not have access to the fal WMA TURN servers. Request access from fal. Without it, the app uses a direct connection with STUN only. |

## Use Your Own Model

The model is the pipeline that `prepare_runtime` loads. The model thread calls `start_stream_session` and `run_stream_batch`.
The rest is the same WMA shape for any model:

* A model thread takes a fixed number of input frames and returns output frames.
* The thread puts each frame in a bounded queue, with the `put_output` method. The queue drops the oldest frame when it is full.
* `make_video_queue_track` plays the queue as the video track, and `AiortcPeer` sends it to the client.

The `read_camera` method shows how camera frames arrive. It keeps only the newest frame, so the camera queue never grows.

```python theme={null}
    async def read_camera(self, track):
        # Drain the camera track so its queue never grows. Keep only the newest frame.
        while True:
            try:
                self.latest = await track.recv()
            except Exception:
                break
        # The camera ended. Stop feeding frames so the model stops generating.
        self.latest = None
        with self.cond:
            self.frames.clear()
        self.set_state("waiting_for_camera")
```

The `put_output` method queues one output frame.

```python theme={null}
    def put_output(self, frame):
        """Queue a frame for playback on the event loop. Count the frame it displaces."""
        if self.out.full():
            self.dropped_output_frames += 1
        fal.wma.queue_put_drop_oldest(self.out, frame)
```

## Billing

This section applies if you [list the app on the fal Marketplace](../../documentation/serverless/publishing-to-marketplace.md). For your own private app, you pay only for runner time, and the units do not change the cost.

The app calls `session.add_billable_units` with the number of frames that it queues for playback.
One unit is one output frame. The warm-up in `setup()` does not count.
Some frames never play. The app drops the oldest frame when the output queue is full, and some frames remain queued when the session ends.
Before billing is final, a deferred cleanup calls `session.cap_billable_units(total - undelivered)`. The argument is the new total, so the undelivered frames are not billed.
The fal team configures the price per unit when it lists the app. See [billing in the WMA reference](../../documentation/development/wma.md#ice-billing-and-deployment).

## Limits

* **Speed.** On one H100 in our tests, the app streamed 512x512 at the full 16 fps. The median frame gap was about 63 ms. The 95th percentile gap was about 110 to 130 ms across two runs.
* **Pauses.** A prompt change restarts the model stream and pauses output for under half a second. Read `output_frames` and `dropped_input_frames` in the `status` messages to measure your own session.
* **Slow model, higher delay.** If the model makes fewer than 16 frames per second, the app drops input frames. The video looks less smooth, but the delay stays bounded.
* **Style strength depends on the prompt.** The `NOISE_SCALE` value of 0.85 keeps faces and motion. In our tests, faces started to change at 0.9, and identity was lost at 0.95. More denoising steps (3 or 4) did not look better than 2.
* **One session for each runner.** Each session owns the GPU. More sessions need more runners, and `max_concurrency` limits the count.
* **Square output.** The app crops the center of the camera frame and outputs 512 by 512. This is the size the upstream web demo uses. The upstream offline default is 480 by 832.
* **No acceleration options.** The app does not enable the upstream TAEHV decoder, TensorRT, or flash-attn. Read the upstream README before you add them.
* **No content moderation.** The app does not check camera frames or prompts. Add checks before you serve public users.
