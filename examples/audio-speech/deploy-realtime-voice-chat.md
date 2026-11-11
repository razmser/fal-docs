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

# Deploy Moshi Real-time Voice Chat with WMA

> Deploy Moshi, a full-duplex speech-to-speech model, as a real-time voice chat app on one H100. Stream audio over WebRTC with the fal WMA SDK.

This example deploys [Moshi](https://github.com/kyutai-labs/moshi), a full-duplex speech-to-speech model from Kyutai, as a real-time voice chat app on one H100 with the [WMA SDK](../../documentation/development/wma.md).
The browser sends its microphone over WebRTC. The app streams Moshi's voice back as a live audio track and sends Moshi's words as text messages.

> [!TIP]
>
> If you want real-time speech from a ready-made API, fal hosts two from Kotoba Technologies: [Kotoba Realtime Speech Translation](https://fal.ai/models/kotoba-technologies/kotoba-sts/v1/realtime_voice) (speak in one language and hear another, with the translated text alongside the audio) and [Kotoba Text-to-Speech](https://fal.ai/models/kotoba-technologies/kotoba-tts/v2/tts/ws) (natural Japanese speech, streamed as it is generated). Nothing to deploy. Use this example when you want to run your own speech-to-speech model.

<video playsInline controls preload="metadata" poster="https://fal.ai/docs/images/examples/wma-voice-chat-poster.jpg">
  <source src="https://mintcdn.com/fal-d8505a2e/e51eBJ8MO88LLecM/images/examples/wma-voice-chat.mp4?fit=max&auto=format&n=e51eBJ8MO88LLecM&q=85&s=48433df5de1675c2a336d9bb3ce651c4" type="video/mp4" />
</video>

The clip is a recording of this app on one H100, with three recorded questions in place of the microphone. Play it with sound. The top lane is what the client sent, and the bottom lane is what the app sent back. The captions show the question and the `text` messages.

> [!WARNING]
>
> The [WMA SDK](../../documentation/development/wma.md) is experimental. Pin the SDK version, as this page does.

## Key Facts

| Item | Value |
| - | - |
| Model | Moshi 7B from Kyutai, in bfloat16 |
| GPU | One H100 |
| Audio | 24 kHz mono, 80 ms frames |
| Reply delay | About one second after the end of each question, network included, in our tests |
| First start | About 3 minutes on a fresh runner, which downloads about 16 GB of weights |
| Session limit | 300 seconds |
| Language | English only |
| License | Weights CC-BY-4.0, `moshi` package MIT |

## What the App Does

The client sends an audio track and gets Moshi's reply back as audio. The [video restyle example](../video-generation/deploy-realtime-video-restyle.md) has the same shape with video.

* It resamples the microphone to 24 kHz mono and cuts it into 80 ms frames.
* Each frame goes to Moshi. Each step returns one 80 ms frame of Moshi's voice and, often, one text token.
* Moshi's voice leaves as an audio track, and each word piece leaves as a `text` message.
* A `reset` message from the client starts a new conversation.
* A `status` message goes out when the state changes, and every 2 seconds.

Moshi listens and speaks at the same time. It does not wait for the end of a sentence, so the caller can interrupt it.
The model is natively real time. Every 80 ms of input produces 80 ms of output, including silence. The app adds no turn detection or text-to-speech step.

## Prerequisites

* A fal account with permission to deploy and call the app. The browser client connects through fal's WMA service at `wma.fal.run`. It needs a fal API key on your server, or a signed-in dashboard session for the playground.
* Access to the fal WMA TURN servers for the account that owns the app (the `X` in `fal.run/X/app`). Request it from fal before you rely on the app. Without it, a session connects only when the runner is directly reachable, which fails on many runners and networks. See [If the Session Does Not Start](#if-the-session-does-not-start).
* Python 3.12 on your computer. The app image uses Python 3.12, and the Python versions must match for deployment.
* The fal CLI and the demo code. Run these commands:

```bash theme={null}
git clone https://github.com/fal-ai-community/fal-demos.git
cd fal-demos
uv venv --python 3.12
source .venv/bin/activate
uv pip install 'fal[wma]==1.82.1'
fal auth login
```

* A browser with a microphone. Microphone capture needs HTTPS or localhost. Use headphones, so Moshi does not hear its own voice.
* One H100 GPU. Moshi is a 7B model in bfloat16.

The weights are public and not gated. They use the CC-BY-4.0 license, and the `moshi` package uses the MIT license.
Each new runner downloads about 16 GB of weights from Hugging Face. The app pins the weights to one revision.

## The App

View the complete source code on [GitHub](https://github.com/fal-ai-community/fal-demos/blob/main/fal_demos/video/wma/voice_chat.py).
This page shows the parts that use the WMA SDK.

### Messages

The client sends one `reset` message. The app sends `text`, `status`, and `rejected` messages.

```python theme={null}
class Reset(BaseModel):
    type: Literal["reset"] = "reset"


class Text(BaseModel):
    type: Literal["text"] = "text"
    text: str = Field(description="One piece of what Moshi is saying, in order.")
# ...
ServerMessage = Annotated[Union[Text, Status, Rejected], Field(discriminator="type")]
```

### The App Class

The class sets the runner, the pinned requirements, and a long `startup_timeout` for the weight download.
The `max_multiplexing` value of 1 gives each session the whole GPU.

```python theme={null}
class VoiceChatApp(fal.wma.App, python_version="3.12"):
    machine_type = "GPU-H100"
    requirements = [
        "fal[wma]==1.82.1",
        "moshi==0.2.13",
# ...
        "aiortc",
    ]
    startup_timeout = 1200  # Weights download and CUDA graph capture take minutes.
    min_concurrency = 0
    max_concurrency = 2
    max_multiplexing = 1  # One session owns the GPU and the streaming state.
    keep_alive = 300
```

`max_concurrency` is the most runners, and `max_multiplexing` is the most sessions on one runner. Both values are examples. Adjust them for your workload and cost. See [Scale your application](../../documentation/deployment/scale-your-application.md).

### The Contract

The contract declares a required microphone track, an audio output track, and one example message.

```python theme={null}
    realtime_contract = fal.wma.RealtimeContract(
        media=fal.wma.MediaContract(
            send=(fal.wma.Track(kind="audio", source="microphone", required=True),),
            receive=(fal.wma.Track(kind="audio"),),
        ),
        client_messages=Reset,
        server_messages=ServerMessage,
        client_message_examples=(
            fal.wma.MessageExample(name="New conversation", payload={"type": "reset"}),
        ),
    )
```

### The Model Step

`Moshi.step` takes one 80 ms input frame and returns the audio and text that Moshi produced for it.
Text tokens 0 and 3 are padding, so the app skips them.

```python theme={null}
    def step(self, chunk, skip=False):
        """Feed one input frame. Return (pcm, text) pairs for the frames Moshi produced.

        `chunk` is float32 audio shaped (1, 1, FRAME_SAMPLES). With `skip`, the
        frame is encoded and then discarded, as upstream does for the first frame.
        """
        codes = self.mimi.encode(chunk)
        if skip:
            # The first encoded frame carries the encoder's left padding. Upstream
            # resets the encoder so the next frame gets the padding again.
            self.mimi.reset_streaming()
        out = []
        for c in range(codes.shape[-1]):
            tokens = self.lm_gen.step(codes[:, :, c : c + 1])
            if tokens is None:  # Moshi has not produced a frame yet.
                continue
            pcm = self.mimi.decode(tokens[:, 1:])[0, 0].float().cpu().numpy()
            token = tokens[0, 0, 0].item()
            text = None
            if token not in TEXT_PADDING_TOKENS:
                text = self.tokenizer.id_to_piece(token).replace("▁", " ")
            out.append((pcm, text))
        return out
```

### The Session Backend

The `create_backend` method attaches the output track when the microphone track arrives.
It also ends the session after `MAX_SESSION_SECONDS`.
The lines that build the ICE servers (the `RunnerIceConfig` call) are boilerplate. Copy them as they are.

```python theme={null}
        def connect(pc):
            attached = False

            @pc.on("track")
            def incoming(track):
                nonlocal attached
                if track.kind == "audio" and not attached:
                    attached = True
                    speaker = make_audio_queue_track(voice.out)
                    pc.addTrack(speaker)
                    voice.start(track, speaker)

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

### Pinned Model

The app loads Moshi with the `moshi` package from Kyutai, as the upstream server does.
The weights come from `kyutai/moshiko-pytorch-bf16`, pinned to one revision with a constant at the top of the file.
All heavy imports run inside the `Moshi` class, so the file imports on a computer without PyTorch.
The streaming state lives for the whole runner and is reset at the start of each conversation.
`setup()` runs four silent frames, so CUDA graph capture happens before the first session.

### Audio In

Browsers send 48 kHz Opus. `read_microphone` resamples every incoming frame to 24 kHz mono with an `av.AudioResampler`, then cuts the audio into 80 ms frames of 1,920 samples.
The input buffer holds at most 4 frames (320 ms). If the model falls behind, the app drops the oldest frames and counts them in `dropped_input_frames`.

The first encoded frame is discarded, as upstream does, because the encoder's left padding makes it look like the past to the model.

### Audio Out

The WMA SDK has a queue track for video, `make_video_queue_track`, but not for audio. The app builds its own audio track, `make_audio_queue_track`.

```python theme={null}
        async def recv(self):
            now = time.monotonic()
            if self.start is None:
                self.start = now
            due = self.start + self.pts / SAMPLE_RATE
            if due < now - 1:  # The consumer stalled. Do not burst to catch up.
                self.start = now - self.pts / SAMPLE_RATE
            elif due > now:
                await asyncio.sleep(due - now)
            while len(self.pending) < PACKET_SAMPLES and not queue.empty():
                self.pending = np.concatenate([self.pending, queue.get_nowait()])
            if len(self.pending) >= PACKET_SAMPLES:
                chunk = self.pending[:PACKET_SAMPLES]
                self.pending = self.pending[PACKET_SAMPLES:]
            else:
                chunk = np.zeros(PACKET_SAMPLES, dtype=np.float32)
```

The track sends 20 ms packets, plays silence when the queue is empty, and rebases its clock after a stall of more than one second. The output queue holds at most 2 seconds. The app drops the oldest audio when the queue is full, with `queue_put_drop_oldest`, and counts it as undelivered. aiortc encodes the 24 kHz mono frames to Opus for the browser.

### Model Thread

The model call blocks, so it runs on its own thread and never on the event loop.
The thread calls `loop.call_soon_threadsafe` to give audio to the output queue.
The `shutdown` function is a deferred cleanup. It stops the thread and waits for the thread to end before the session closes.
The `cap_billing` function runs after `shutdown` and before billing is final.
The app sets `max_multiplexing = 1` because the model keeps one streaming state, and a lock protects it.

## Contract and Client Controls

The `realtime_contract` describes the session for the playground and for client code.

| Contract field | Value | Client control |
| - | - | - |
| `media.send` | Required microphone audio track | Pass your microphone stream as `localStream`. |
| `media.receive` | One audio track | Pass `receive: ["audio"]`. |
| `client_messages` | `reset` | Send `{ type: "reset" }`. |
| `server_messages` | `text`, `status`, and `rejected` | Read the messages in `onData`. |

The app does not use an interaction profile, because its only control is `reset`.

## Run and Deploy

From the root of the cloned repository, use `fal run` as the development loop. It starts the app on an ephemeral runner:

```bash theme={null}
fal run fal_demos/video/wma/voice_chat.py::VoiceChatApp
```

To deploy the app as a private app, run the next command. Use the deployed app for the playground and the browser client below.

```bash theme={null}
fal deploy fal_demos/video/wma/voice_chat.py::VoiceChatApp --auth private
```

### Cost and First Start

You pay per second for the runner, including setup time and the idle `keep_alive` time. See [Pricing](../../documentation/serverless/pricing.md). The billable units in the app matter only if you [list the app on the fal Marketplace](../../documentation/serverless/publishing-to-marketplace.md). Callers then pay for these units at a price that the fal team sets.

The first session starts a runner. The runner downloads about 16 GB of weights and captures CUDA graphs. On a fresh runner this took about 3 minutes in our tests.
Watch the runner logs in the dashboard.
To keep a runner warm, set `min_concurrency = 1`. A warm runner costs idle time. See [cold starts](../../documentation/serverless/optimizations/cold-start-scaling.md).

### Clean Up

To stop paying for the app, scale it to zero with `fal apps scale <app-name> --min-concurrency 0`. To remove it, run `fal apps delete <app-name>`.

The idle runner timeout (`keep_alive`) is 300 seconds. A runner stays up for that time after its last session.
The maximum session length is also 300 seconds (`MAX_SESSION_SECONDS`). Change the constant to change the limit.

## Try It

### Playground

1. Open the deployed app in the fal dashboard.
2. Select its `/start-session` playground.
3. Select **Connect** and allow the microphone.
4. Start talking. Moshi often speaks first.
5. Read the `text` messages in the message log. They spell out what Moshi is saying.
6. Send **New conversation** to reset Moshi.
7. Select **Disconnect** to end the session.

### Browser Client

The experimental [WMA browser client](../../documentation/development/wma-browser.md) sends the microphone and the `reset` message. There is no Python or Node client today.
The deploy output shows the app ID as `your-account/<app-name>`. The name comes from the class name, so `VoiceChatApp` becomes `voice-chat-app`.
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

export async function connectVoiceChat(
  appId: string,
  speaker: HTMLAudioElement,
  onText: (text: string) => void,
  onEnded: (reason: string) => void,
): Promise<{ reset(): void; close(): Promise<void> }> {
  const microphone = await navigator.mediaDevices.getUserMedia({
    audio: { echoCancellation: true, noiseSuppression: true },
    video: false,
  });
  const stopMicrophone = () => microphone.getTracks().forEach((track) => track.stop());
  let ended = false;
  const finish = (reason: string) => {
    // A failure reaches both onError and onState, so run the cleanup once.
    if (ended) return;
    ended = true;
    stopMicrophone();
    speaker.srcObject = null;
    onEnded(reason);
  };
  const session = fal.realtime.open(wma(appId), {
    localStream: microphone,
    receive: ["audio"],
    onMedia(stream) {
      speaker.srcObject = stream;
      void speaker.play().catch(console.error);
    },
    onData: (raw) => {
      const msg = JSON.parse(raw);
      switch (msg.type) {
        case "text":
          onText(msg.text);
          break;
        case "status":
          console.log(msg.state, msg.heard_seconds, msg.dropped_input_frames);
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
    stopMicrophone();
    await session.close();
    throw error;
  }
  return {
    reset: () => session.send({ type: "reset" }),
    async close() {
      try {
        await session.close();
      } finally {
        stopMicrophone();
        speaker.srcObject = null;
      }
    },
  };
}
```

The `onData` callback receives the raw JSON string, so parse it first. Each `text` message is one word piece, often with a leading space, so append the pieces in order. Browsers can block audio playback until the user interacts with the page, so call `connectVoiceChat` from a click handler. On a closed or failed session, the code stops the microphone and calls `onEnded`. The `ended` flag makes that cleanup run once. The app closes each session after 300 seconds, so expect `onEnded` even when nothing fails.

### Server Messages

| Message | Example |
| - | - |
| `text` | `{"type": "text", "text": " Paris"}` |
| `status` | `{"type": "status", "state": "listening", "heard_seconds": 23.2, "spoken_seconds": 23.12, "dropped_input_frames": 0}` |
| `rejected` | `{"type": "rejected", "reason": "generation failed"}` |

The `state` field of `status` is `waiting_for_microphone`, `listening`, or `restarting`.

## If the Session Does Not Start

| Symptom | Cause and action |
| - | - |
| `401` with a body such as `{"error": "missing Authorization header"}` or `{"error": "account is locked"}` | The request has no valid credential, or the account is locked. Check the proxy and your fal API key. |
| The playground reports "relay 0 from 0 TURN servers offered", and ICE stays in `checking` | The app owner does not have access to the fal WMA TURN servers. Request access from fal. Without it, the session connects only when the runner is directly reachable. |

## Use Your Own Model

The model is the `Moshi` class. The model thread calls `Moshi.step` with one input frame and plays the audio frames it returns.
The rest is the same WMA shape for any streaming audio model:

* A model thread takes a fixed amount of input audio and returns output audio.
* The thread puts each output chunk in a bounded queue, with the `put_output` method. The queue drops the oldest chunk when it is full.
* `make_audio_queue_track` plays the queue as the audio track, and `AiortcPeer` sends it to the client.

To serve a cascaded voice agent (speech recognition, a language model, and text-to-speech), keep the same input and output code and replace `Moshi.step` with your pipeline.
A cascade waits for the caller to finish a sentence, so add turn detection and expect a longer delay before each reply.

The `put_output` method queues one output chunk.

```python theme={null}
    def put_output(self, pcm):
        """Queue audio for playback on the event loop. Count the audio it displaces."""
        if self.out.full():
            self.dropped_output_samples += len(self.out._queue[0])
        fal.wma.queue_put_drop_oldest(self.out, pcm)
```

## Billing

This section applies if you [list the app on the fal Marketplace](../../documentation/serverless/publishing-to-marketplace.md). For your own private app, you pay only for runner time, and the units do not change the cost.

The app calls `session.add_billable_units` with the seconds of audio that it queues for playback.
One unit is one second of Moshi's output audio, silence included. The warm-up in `setup()` does not count.
Some audio never plays. The app drops the oldest audio when the output queue is full, and some audio remains queued when the session ends.
Before billing is final, a deferred cleanup calls `session.cap_billable_units(total - undelivered)`. The argument is the new total, so the undelivered audio is not billed.
The fal team configures the price per unit when it lists the app. See [billing in the WMA reference](../../documentation/development/wma.md#ice-billing-and-deployment).

## Limits

* **Speed.** On one H100, the app kept up with real time over a 27 second conversation with no dropped input frames. Moshi's replies started about one second after the end of each question, network included.
* **Slow model, dropped audio.** If a step takes longer than 80 ms, the app drops input frames. The conversation gets choppy, but the delay stays bounded.
* **Transcript gaps.** Moshi's text stream can skip a word piece that its audio says, for example `I' sorry` for "I'm sorry". The audio is not affected.
* **Echo.** Moshi hears whatever the microphone picks up, including its own voice from speakers. Use headphones or browser echo cancellation.
* **Generation failure.** The app sends a `rejected` message with the reason "generation failed". It then closes the session.
* **Microphone ends.** When the microphone track ends, the app returns to `waiting_for_microphone` and stops generating.
* **One voice and persona.** The `moshiko` weights have one male voice and no system prompt. Kyutai publishes a female voice as `kyutai/moshika-pytorch-bf16`.
* **English only.** Moshi understands and speaks English.
* **No content moderation.** The app does not check what the caller says or what Moshi says. Add checks before you serve public users.
