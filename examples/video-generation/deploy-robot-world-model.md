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

# Deploy NVIDIA Cosmos Robot World Model with WMA

> Deploy NVIDIA Cosmos3-Edge-Policy-DROID, a robot policy and world model, on one H100. Send an instruction and robot state, and get actions and predicted video.

This example deploys [Cosmos3-Edge-Policy-DROID](https://huggingface.co/nvidia/Cosmos3-Edge-Policy-DROID), a 4B robot policy and world model from NVIDIA, on one H100 with the [WMA SDK](../../documentation/development/wma.md).
For each text instruction and robot state, the model returns a 32-step action chunk and a predicted video of what the robot will see.
It works only with a DROID-style robot.

<video autoPlay loop muted playsInline controls>
  <source src="https://mintcdn.com/fal-d8505a2e/e51eBJ8MO88LLecM/images/examples/wma-robot-world-model.mp4?fit=max&auto=format&n=e51eBJ8MO88LLecM&q=85&s=4fb4d6049aff35a83feeb6da533bd344" type="video/mp4" />
</video>

The clip above is the predicted video from this app on one H100, one chunk after another. The top view is the wrist camera and the bottom views are the two external cameras. The caption shows the `step` message and the instruction behind each chunk. The instruction starts as "Put the marker in the cup." and changes to "Open the drawer." after six chunks.

> [!WARNING]
>
> The [WMA SDK](../../documentation/development/wma.md) is experimental. Pin the SDK version, as the app below does.

## Key Facts

| Item | Value |
| - | - |
| Model | Cosmos3-Edge-Policy-DROID from NVIDIA, 4B parameters, OpenMDW 1.1 license |
| GPU | One H100, about 11.6 GB of peak GPU memory in our tests |
| Checkpoint | About 9 GB |
| Output for each chunk | 32 actions (7 joint positions and 1 gripper value each) and 33 frames at 640x528 |
| Chunk time | About 0.9 seconds warm, about 3 seconds for the first call, in our tests |
| Control rate | 15 Hz, about 2.1 seconds of motion for each chunk |
| Session limit | 15 minutes |
| Sessions | One session for each runner |

## What the App Does

The client sends a text instruction, an observation image, and the robot joint state.
For each `step` message, the app returns two outputs:

* An **action chunk** on the control channel (the JSON message channel that runs next to the video). It has 32 steps, and each step has 7 joint positions and 1 gripper value (DROID, D=8).
* The **predicted video** for that chunk on the outgoing WebRTC track, at 15 frames per second.

This is chunked streaming, not per-frame streaming. The app does one model call for each chunk.
On one H100 in our tests, a warm 32-step chunk took about 0.9 seconds from `step` to `action_chunk`. The first call after start took about 3 seconds.
Each chunk returns 33 predicted frames at 640x528. The first frame appears to repeat the observation. The app plays all 33 frames of the first chunk. For later chunks it skips the first frame, so 32 frames play for about 2.1 seconds.

The app shows the `ConfiguredSession` interaction profile from [`fal.wma.profile`](../../documentation/development/wma.md#declare-client-interactions):

1. The client sends `configure` with the first instruction and the initial state.
2. The app sends `configured`.
3. The client sends `step` messages. It can also send `instruction` messages to change the task.

The app applies the newest instruction when the next chunk starts. It rejects an update whose version is not newer than the current one.

Each row of `actions` has 7 joint positions in radians and 1 gripper value from 0 to 1. The arm is a DROID (Franka-style) arm.
The rows use the same joint space as the `joint_position` input. The control rate is 15 Hz.
One chunk has 32 rows, so one chunk covers about 2.1 seconds of open-loop motion.

## Prerequisites

* A fal account with permission to deploy and call the app. The browser client connects through fal's WMA service at `wma.fal.run`. It needs a fal API key on your server, or a signed-in dashboard session for the playground.
* Access to the fal WMA TURN servers for the account that owns the app (the `X` in `fal.run/X/app`). Request it from fal before you rely on the app. Without it, a session connects only when the runner is directly reachable, which fails on many runners and networks. See [If the Session Does Not Start](#if-the-session-does-not-start).
* Python 3.13 on your computer. The app image uses Python 3.13, and the Python versions must match for deployment.
* The fal CLI and the demo code. Run these commands:

```bash theme={null}
git clone https://github.com/fal-ai-community/fal-demos.git
cd fal-demos
uv venv --python 3.13
source .venv/bin/activate
uv pip install 'fal[wma]==1.82.1'
fal auth login
```

* A DROID-style observation image at an `http` or `https` URL. Put the wrist camera view on top, and the two exterior camera views side by side below it. The model trained on this layout.

The app downloads the image with an SSRF-safe client, up to 20 MB. It accepts a URL only, and it does not accept a data URI.
The app reads the image as RGB and passes it to the model without resizing it.

The model weights are public and not gated. The repository uses the OpenMDW 1.1 license, which allows commercial use.
The upstream code is [NVIDIA/cosmos-framework](https://github.com/NVIDIA/cosmos-framework), pinned to one commit in the app.

### GPU and Runtime

The app uses one `GPU-H100` runner. The checkpoint is about 9 GB, and the model peaked at about 11.6 GB of GPU memory in our tests.
NVIDIA does not publish a minimum VRAM figure. The model card lists H100 as a tested platform.
The app does not need vLLM-Omni. It uses the PyTorch service from the Cosmos framework.

The Cosmos framework needs PyTorch 2.13 with CUDA 13 and a Python 3.13 environment. The app builds this environment in a [custom container](../../documentation/development/use-custom-container-image.md).
The first image build is slow. The first start downloads the weights to `/data/hf`.
The Dockerfile starts from a CUDA 13 base image and installs `uv`, the Cosmos framework, and `fal[wma]`:

```python theme={null}
DOCKERFILE = f"""
FROM nvidia/cuda:13.0.2-cudnn-devel-ubuntu24.04
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \\
    ca-certificates curl ffmpeg git libgl1 libglib2.0-0 && rm -rf /var/lib/apt/lists/*
# ...
RUN uv pip install 'fal[wma]==1.82.1'
"""
```

The [custom container page](../../documentation/development/use-custom-container-image.md) describes other ways to build an image.

## The App

View the complete source code on [GitHub](https://github.com/fal-ai-community/fal-demos/blob/main/fal_demos/video/wma/robot_world_model.py).
This page shows the parts that use the WMA SDK.

### The App Class

The class builds a custom container from a Dockerfile string, because the Cosmos framework needs its own PyTorch and CUDA version.
It also sets the runner and a long `startup_timeout` for the weight download.

```python theme={null}
class RobotWorldModel(fal.wma.App):
    machine_type = "GPU-H100"
    image = ContainerImage.from_dockerfile_str(DOCKERFILE)
    min_concurrency = 0
    max_concurrency = 1
    max_multiplexing = 1
    keep_alive = 300
    startup_timeout = 1800
```

The `min_concurrency = 0` value scales the app to zero, so the first session waits for a cold start. The `keep_alive` value is the idle seconds that a runner stays up after its last session, and fal bills this time. The `max_concurrency` and `max_multiplexing` values are the most runners and the most sessions on one runner. They are example limits. Adjust them for your workload and cost. See [Scale your application](../../documentation/deployment/scale-your-application.md).

### The Contract

The contract declares one video track, the typed messages, and the `ConfiguredSession` profile.

```python theme={null}
    realtime_contract = fal.wma.RealtimeContract(
        media=fal.wma.MediaContract(receive=(fal.wma.Track(kind="video"),)),
        client_messages=ClientMessage,
        server_messages=ServerMessage,
    # ...
        interaction=profile.ConfiguredSession(
            setup=ConfigureMessage,
            ready=profile.Event(ConfiguredMessage, correlation="/instruction_version"),
            sequence=profile.Sequence(field="/instruction_version"),
```

### The Message Handlers

The `instruction` handler keeps the newest text and sends `instruction_pending` or `instruction_rejected`.

```python theme={null}
    def on_instruction(self, raw):
        if not self.configured:
            return self.fail("not_configured", "Send configure first.")
        try:
            message = InstructionMessage.model_validate(raw)
        except ValidationError as exc:
            version = raw.get("instruction_version")
            if isinstance(version, int):
                return self.send(
                    InstructionRejectedMessage(
                        instruction_version=version, reason="invalid_instruction"
                    )
                )
            return self.invalid(raw, exc)
        newest = max(self.version, self.pending[0] if self.pending else 0)
        if message.instruction_version <= newest:
            return self.send(
                InstructionRejectedMessage(
                    instruction_version=message.instruction_version,
                    reason="stale_instruction_version",
                )
            )
        # The newest text wins. It reaches the model at the start of the next chunk.
        self.pending = (message.instruction_version, message.instruction)
        self.send(InstructionPendingMessage(instruction_version=message.instruction_version))
```

### The Session Backend

The `create_backend` method registers one handler for each message and ends the session after `SESSION_SECONDS`.
The lines that build the ICE servers (the `RunnerIceConfig` call) are boilerplate. Copy them as they are.

```python theme={null}
        frames = asyncio.Queue(maxsize=FRAME_QUEUE_SIZE)
        robot = RobotSession(
            session, self.service, self.executor, self.observation, frames
        )
        session.on_message("configure", robot.on_configure)
        session.on_message("instruction", robot.on_instruction)
        session.on_message("step", robot.on_step)
        session.defer(robot.shutdown)

        async def expire():
            await asyncio.sleep(SESSION_SECONDS)
            await session.close()

        session.create_task(expire())
        track = fal.wma.make_video_queue_track(frames, FPS)
        return fal.wma.AiortcPeer(
            session,
            lambda pc: pc.addTrack(track),
            rtc_configuration=RTCConfiguration(iceServers=servers),
            disconnected_grace_seconds=10,
        )
```

## How the Contract Maps to Client Controls

| Client sends | App answers | Meaning |
| - | - | - |
| `configure` | `configured` | Sets the instruction, observation image, and start state. Send it once. |
| `step` | `action_chunk` and video | Generates one chunk. |
| `instruction` | `instruction_pending` or `instruction_rejected`, then `instruction_applied` at the next step | Changes the task. The newest version wins. A replaced pending update gets no further event. |

Send `step_id` as a positive integer. The app copies it into the `action_chunk` message.
The `instruction_version` values must increase. The first value is in `configure`.

A `step` can carry `joint_position`, `gripper_position`, and `image_url`.
When the client sends them, the model starts from the real robot state and camera view.
When the client omits them, the app continues from the last predicted action and the last predicted frame.
This second mode is an imagined rollout. Prediction errors add up over many chunks.

Send one `step` at a time. Send the next `step` only after the `action_chunk` for the previous step arrives.
A `step` that arrives while a chunk generates returns an error with code `busy`.
A chunk takes about 0.9 seconds to generate. It covers about 2.1 seconds of robot time, so a loop with no wait runs ahead of playback.
The video queue holds four chunks and drops the oldest frames when it is full.
For video that stays near real time, send the next `step` when the robot finishes the chunk.

The video and the actions are not time-aligned on the wire. Use `step_id` to match an action chunk to its step. Treat the video as a preview.

Fatal errors have the codes `invalid_configuration`, `invalid_image`, and `generation_failed`. The app closes the session after them.
An invalid `image_url` in `configure` or in `step` gives `invalid_image`.

## Run and Deploy

From the root of the cloned repository, use `fal run` as the development loop. It starts the app on an ephemeral runner:

```bash theme={null}
fal run fal_demos/video/wma/robot_world_model.py::RobotWorldModel
```

Deploy the app as a private app. Use the deployed app for the playground and the browser client below.

```bash theme={null}
fal deploy fal_demos/video/wma/robot_world_model.py::RobotWorldModel --auth private
```

### Cost and First Start

You pay per second for the runner, including setup time and the idle `keep_alive` time. See [Pricing](../../documentation/serverless/pricing.md). The billable units in the app matter only if you [list the app on the fal Marketplace](../../documentation/serverless/publishing-to-marketplace.md). Callers then pay for these units at a price that the fal team sets.

The `fal run` and `fal deploy` commands build the container image. In our tests, the build took about 3 minutes.
The first session starts a runner and downloads the checkpoint of about 9 GB.
Watch the runner logs in the dashboard. The `/data` volume keeps the weights across runners. See [persistent storage](../../documentation/development/use-persistent-storage.md).
To keep a runner warm, set `min_concurrency = 1`. A warm runner costs idle time. See [cold starts](../../documentation/serverless/optimizations/cold-start-scaling.md).

### Clean Up

To stop paying for the app, scale it to zero with `fal apps scale <app-name> --min-concurrency 0`. To remove it, run `fal apps delete <app-name>`.

## Try It

Open the deployed app's `/start-session` page in the fal playground and select **Connect**.
The playground reads the app contract and offers the example messages.

1. Send the **Configure** example. The example `image_url` is a sample DROID observation. It combines frame 0 of the three camera views from the `droid_lerobot_example` assets in the pinned cosmos-framework commit. The fal CDN hosts it. Replace it with a public URL of your own DROID-style image.
2. Wait for the `configured` message.
3. Send **Step from predicted state**. The app answers with an `action_chunk` message and sends the predicted video.
4. Send **New instruction**, then send another step.

### Browser Client

There is no supported Python or Node client. Contact fal if you need one.
The supported client is the experimental [WMA browser client](../../documentation/development/wma-browser.md). It runs in a browser.

1. Set up the server proxy as the [browser client page](../../documentation/development/wma-browser.md#install-the-client-and-server-proxy) describes.
2. Install the client and the proxy package in your web application.

```bash theme={null}
npm install @fal-ai/client@1.11.0-alpha.5 @fal-ai/server-proxy@1.2.1
```

3. Add the code below. The API key stays on your server behind the proxy route.

The deploy output shows the app ID as `your-account/<app-name>`. The name comes from the class name, so `RobotWorldModel` becomes `robot-world-model`.
Use `receive: ["video"]` and no `localStream`. The `onData` callback receives the raw JSON string. Parse it before you use it.
The code waits for `configured` before the first `step`, then sends each next `step` when the robot finishes.
If `executeActions` fails, the code closes the session. The `ended` flag makes the cleanup run once.

```typescript theme={null}
import { fal } from "@fal-ai/client";
import { wma } from "@fal-ai/client/realtime/wma";

fal.config({ proxyUrl: "/api/fal/proxy" });

// The element needs the muted and playsinline attributes for autoplay: <video id="output" muted playsinline>
const video = document.querySelector<HTMLVideoElement>("#output")!;
let stepId = 0;
let ended = false;

// Your own code. It runs one chunk on the robot and resolves when the robot finishes.
declare function executeActions(actions: number[][]): Promise<void>;

function endSession(message: string) {
  // A failure reaches both onError and onState, so run the cleanup once.
  if (ended) return;
  ended = true;
  video.srcObject = null;
  console.log(message);
}

function sendStep() {
  if (ended) return;
  stepId += 1;
  // Add joint_position, gripper_position, and image_url from your robot to start from its real state.
  session.send({ type: "step", step_id: stepId });
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
      case "configured":
        sendStep();
        break;
      case "action_chunk":
        // Match the actions to the step with msg.step_id. Treat the video as a preview.
        executeActions(msg.actions)
          .then(sendStep)
          .catch((error) => {
            console.error(error);
            endSession("Robot action failed.");
            void session.close();
          });
        break;
      case "error":
        console.error(msg.code, msg.error);
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
session.send({
  type: "configure",
  instruction_version: 1,
  instruction: "Put the marker in the cup.",
  image_url: "https://raw.githubusercontent.com/fal-ai-community/fal-demos/main/fal_demos/video/wma/droid_observation.png",
});
```

The app does not connect to a robot. Your own code must read the robot state, run the returned actions, and send the next `step`.
A session can end on its own. The app closes each session after 15 minutes, and it closes the session after a fatal error.

### Server Messages

| Message | Example |
| - | - |
| `configured` | `{"type": "configured", "instruction_version": 1, "chunk_steps": 32, "action_dim": 8, "fps": 15}` |
| `instruction_pending` | `{"type": "instruction_pending", "instruction_version": 2}` |
| `instruction_applied` | `{"type": "instruction_applied", "instruction_version": 2}` |
| `instruction_rejected` | `{"type": "instruction_rejected", "instruction_version": 2, "reason": "stale_instruction_version"}` |
| `action_chunk` | `{"type": "action_chunk", "step_id": 1, "instruction_version": 1, "chunk_index": 0, "actions": [[0.0, -0.78, 0.0, -2.35, 0.0, 1.57, 0.78, 0.0], "..."], "fps": 15, "frames": 33, "generation_seconds": 0.9}` |
| `error` | `{"type": "error", "code": "busy", "error": "A chunk is still generating.", "step_id": 2}` |

The `actions` array is truncated in the example. It has 32 rows of 8 values.

## If the Session Does Not Start

| Symptom | Cause and action |
| - | - |
| `401` with a body such as `{"error": "missing Authorization header"}` or `{"error": "account is locked"}` | The request has no valid credential, or the account is locked. Check the proxy and your fal API key. |
| The connection never leaves `checking`, or fails on a restrictive network | The app owner does not have access to the fal WMA TURN servers. Request access from fal. Without it, the app uses a direct connection with STUN only. |

## Billing

This section applies if you [list the app on the fal Marketplace](../../documentation/serverless/publishing-to-marketplace.md). For your own private app, you pay only for runner time, and the units do not change the cost.

The app calls `session.add_billable_units(1)` after each completed chunk. One unit is one action chunk.
The fal team configures the price per unit when it lists the app. See [billing in the WMA reference](../../documentation/development/wma.md#ice-billing-and-deployment).
Runner compute can also incur charges. Disconnect when you finish.

## Limits and Caveats

* The app serves one session at a time on each runner (`max_multiplexing = 1`).
* The model works only for the DROID robot (8 values). It expects the three-view image layout.
* Add your own validation and safety limits before a real robot runs the actions.
* A model call that already started cannot stop. If the session ends, the app discards the result and does not bill it. The next session's model call waits until that call finishes.
