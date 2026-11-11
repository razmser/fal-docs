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

# Connect a browser to fal WMA

> Connect to a deployed WMA app, receive video, send controls, and clean up the session with the JavaScript client.

Deploy the [generated-video app](wma.md) before starting this walkthrough.
Use its application ID, such as `your-account/your-video-app`. Do not append `/start-session` or supply a URL.

> [!WARNING]
>
> The WMA browser API is experimental. This walkthrough uses
> `@fal-ai/client@1.11.0-alpha.5`, independently of the stable Python `fal`
> package. The JavaScript `latest` version does not necessarily include these
> APIs. Pin the version shown below.

## Install the client and server proxy

Run this command in your web application:

```bash theme={null}
npm install @fal-ai/client@1.11.0-alpha.5 @fal-ai/server-proxy@1.2.1
```

Keep `FAL_KEY` in your server environment. Never put the key in browser code or a `NEXT_PUBLIC_` environment variable.
The browser sends authenticated requests through your server proxy. The proxy adds the fal credential.

For a Next.js App Router application, create `app/api/fal/proxy/route.ts`:

```typescript theme={null}
import { createRouteHandler } from "@fal-ai/server-proxy/nextjs";

export const { GET, POST, PUT } = createRouteHandler({
  allowedUrlPatterns: ["wma.fal.run/**", "fal.run/**"],
});
```

The explicit URL patterns permit WMA signaling and the app ICE fallback. The default proxy patterns do not include `wma.fal.run`.
This minimal route is suitable for a local development app.
Before exposing it, require your application's user authentication and authorization on the route.
Limit access to the endpoints that your application needs, and apply appropriate rate limits.
The proxy's fal credential does not authenticate your application's users.

WMA signaling needs streaming responses. Keep response streaming enabled on your proxy and hosting platform.
The Next.js Pages Router proxy does not support streaming responses. Use the App Router integration above.

## Receive video and send controls

Add these elements to a page:

```html theme={null}
<button id="connect">Connect</button>
<button id="sunset" disabled>Fast sunset</button>
<button id="disconnect" disabled>Disconnect</button>
<p id="status" role="status">Disconnected</p>
<video id="output" autoplay muted playsinline controls></video>
<pre id="messages"></pre>
```

Run the following TypeScript in the browser after the elements exist. Use your application's bundler to resolve the package imports.
Replace the application ID with the ID from your deployment.

```typescript theme={null}
import { fal } from "@fal-ai/client";
import { wma } from "@fal-ai/client/realtime/wma";

fal.config({ proxyUrl: "/api/fal/proxy" });

const appId = "your-account/your-video-app";
const connectButton = document.querySelector<HTMLButtonElement>("#connect")!;
const sunsetButton = document.querySelector<HTMLButtonElement>("#sunset")!;
const disconnectButton =
  document.querySelector<HTMLButtonElement>("#disconnect")!;
const status = document.querySelector<HTMLElement>("#status")!;
const video = document.querySelector<HTMLVideoElement>("#output")!;
const messages = document.querySelector<HTMLElement>("#messages")!;

function openSession() {
  return fal.realtime.open(wma(appId), {
    receive: ["video"],
    onState(state) {
      status.textContent = state;
      sunsetButton.disabled = state !== "live";
    },
    onMedia(stream) {
      video.srcObject = stream;
      void video.play().catch(() => {
        status.textContent = "Use the video play button to start playback.";
      });
    },
    onData(raw) {
      // Display the latest response without interpreting it as HTML.
      messages.textContent = raw;
    },
    onError(error) {
      console.error(error);
      status.textContent = "Connection failed. Disconnect, then try again.";
    },
  });
}

let session: ReturnType<typeof openSession> | undefined;

async function disconnect() {
  const current = session;
  session = undefined;
  disconnectButton.disabled = true;
  sunsetButton.disabled = true;
  try {
    await current?.close();
  } finally {
    video.srcObject = null;
    connectButton.disabled = false;
    sunsetButton.disabled = true;
    disconnectButton.disabled = true;
  }
}

connectButton.addEventListener("click", async () => {
  if (session) return;
  connectButton.disabled = true;
  disconnectButton.disabled = false;
  let opening: ReturnType<typeof openSession> | undefined;
  try {
    opening = openSession();
    session = opening;
    await opening.ready;
  } catch (error) {
    console.error(error);
    // An older connection must not close a replacement session.
    if (session === opening) await disconnect();
  }
});

sunsetButton.addEventListener("click", () => {
  session?.send({ type: "scene", palette: "sunset", speed: 3 });
});

disconnectButton.addEventListener("click", () => {
  void disconnect();
});

window.addEventListener("pagehide", () => {
  void session?.close();
});
```

Select **Connect**, then **Fast sunset**. The app updates the video and acknowledges the command in the message area.
Select **Disconnect** to release the session. In a component framework, also call `close()` when the component unmounts.

`fal.realtime.open()` returns a handle immediately. Its `ready` promise resolves when the session is ready.
Use `onState` for lifecycle changes, `onData` for raw message strings, and `onMedia` for incoming streams.
Parse and validate `onData` messages before your application uses their values.

## Session states

A session has four states. The session moves from `opening` to `live`, and then ends in `failed` or `closed`.
`fal.realtime.open()` returns the session in the `opening` state. `onState` reports each later change.
Both end states are final. A failed session does not change to `closed` later.

| State | What happens |
| - | - |
| `opening` | The client sends its offer to the WMA bridge. The bridge gets a runner, and the runner returns its answer. |
| `live` | A runner accepted the session, and the `ready` promise resolves. The WebRTC connection to the runner is not complete yet. |
| `failed` | The session did not open, or the connection failed after the `live` state. `onError` receives the error one time. |
| `closed` | Your application called `close()` or aborted the session. If the session did not become live, `ready` rejects. `onError` does not fire. |

### Show connection progress

Your UI can show three phases:

1. **Waiting for a runner.** The state is `opening`. The client prepares its network candidates, and then the bridge gets a runner. If no runner is warm, this phase includes the cold start of a runner. The client does not tell you if it waits for capacity or for a runner to start.
2. **Connecting.** The state is `live`. A runner accepted the session. The browser and the runner complete the WebRTC connection.
3. **Streaming.** The video element starts to play the media.

For apps that return media, `onMedia` can fire just before or just after the `live` state.
Attach the stream when `onMedia` delivers it. Use the `playing` event of the video element to find when the media plays.

You can call `send()` before the connection is complete. The client keeps up to 64 messages and sends them in order when the control channel opens.
If you send more, the client drops the oldest messages.

### Limit the wait for a runner

By default, the client does not set a deadline for the bridge to get a runner.
To stop the wait after a fixed time, set `negotiationTimeoutMs`.
This version of the `openSession()` function from the walkthrough also shows the three phases:

```typescript theme={null}
let streaming = false;

video.addEventListener("playing", () => {
  streaming = true;
  status.textContent = "Streaming";
});

function openSession() {
  streaming = false;
  // open() returns in the opening state, and onState does not fire for it.
  status.textContent = "Waiting for a runner";
  return fal.realtime.open(wma(appId), {
    receive: ["video"],
    negotiationTimeoutMs: 60_000,
    onState(state) {
      if (state === "live" && !streaming) status.textContent = "Connecting";
      if (state === "closed") status.textContent = "Disconnected";
      sunsetButton.disabled = state !== "live";
    },
    onMedia(stream) {
      video.srcObject = stream;
      void video.play().catch(() => {
        status.textContent = "Use the video play button to start playback.";
      });
    },
    onData(raw) {
      messages.textContent = raw;
    },
    onError(error) {
      console.error(error);
      status.textContent = "Connection failed. Disconnect, then try again.";
    },
  });
}
```

A failed session does not report `closed`. After a failure, the status keeps the error text until the next connection.

The timeout starts when the client sends its offer. The timeout stops when the answer of the runner arrives.
The timeout does not limit the WebRTC connection after the answer.
If no runner answers before the timeout, the session changes to `failed`, and `onError` receives the error.
To cancel from your UI, call `close()` or use an `abortSignal`. The session then changes to `closed`.

## Choose the media direction

The client options must match the deployed app's contract. These directions are from the browser's perspective:

| App | `localStream` | `receive` | Example control |
| - | - | - | - |
| Echo | Omit | `[]` | `{ type: "echo", text: "Hello, WMA!" }` |
| Generated video | Omit | `["video"]` | `{ type: "scene", palette: "sunset", speed: 3 }` |
| Camera effects | Camera stream | `["video"]` | `{ type: "effect", mode: "edges", strength: 1 }` |
| Motion measurements | Camera stream | `[]` | `{ type: "sensitivity", threshold: 0.15 }` |

`receive: []` creates a session without incoming media tracks. Control messages can still travel in both directions.
For audio output, request `receive: ["audio"]` and attach the received stream to an audio element.

## Send camera video

Call `getUserMedia()` from an explicit user action, such as a Connect button.
Camera capture requires HTTPS or localhost and browser permission.

This function connects to the camera-effects example. It returns a cleanup function for your Disconnect button or component unmount handler.

```typescript theme={null}
import { fal } from "@fal-ai/client";
import { wma } from "@fal-ai/client/realtime/wma";

fal.config({ proxyUrl: "/api/fal/proxy" });

export async function connectCamera(
  appId: string,
  output: HTMLVideoElement,
): Promise<() => Promise<void>> {
  const camera = await navigator.mediaDevices.getUserMedia({
    video: true,
    audio: false,
  });
  const stopCamera = () => camera.getTracks().forEach((track) => track.stop());
  let closeSession: (() => Promise<void>) | undefined;
  try {
    const session = fal.realtime.open(wma(appId), {
      localStream: camera,
      receive: ["video"],
      onMedia(stream) {
        output.srcObject = stream;
        void output.play().catch(console.error);
      },
      onData: console.log,
      onError: console.error,
      onState(state) {
        if (state === "closed" || state === "failed") {
          stopCamera();
          output.srcObject = null;
        }
      },
    });
    closeSession = () => session.close();
    await session.ready;
    session.send({ type: "effect", mode: "edges", strength: 1 });
    return async () => {
      try {
        await session.close();
      } finally {
        stopCamera();
        output.srcObject = null;
      }
    };
  } catch (error) {
    stopCamera();
    output.srcObject = null;
    await closeSession?.();
    throw error;
  }
}
```

The client does not own your camera stream. Stop its tracks when you finish, including after connection failures.
Disable repeated Connect actions while opening. Retain the returned cleanup function and call it when leaving the page.
For a UI that cancels during opening, retain the session handle immediately and call `close()` before `ready` resolves.

For motion measurements, change `receive` to `[]` and use the motion app ID.
Read its JSON measurements through `onData`. That app does not return video.

## Verify network and lifecycle behavior

The WMA extension requests ICE configuration through the authenticated bridge.
To test a TURN-only path, add `iceTransportPolicy: "relay"` to the options passed to `fal.realtime.open()`.
This test requires working TURN provisioning. Do not substitute a public STUN server for TURN.

Use the returned session's connection information when diagnosing network failures:

```typescript theme={null}
// After the video example's session.ready resolves:
if (session?.session) {
  console.log(await session.session.getConnectionInfo());
}
```

Disconnect before creating a replacement session. Keep the endpoint ID and each app's media contract consistent.
For server cleanup and billing checks, see the [WMA deployment guide](wma.md#ice-billing-and-deployment).
