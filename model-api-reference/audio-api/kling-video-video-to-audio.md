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

# Kling Video Video To Audio API

> API reference for Kling Video Video To Audio. Generate audio from input videos using Kling

**Endpoint:** `POST https://fal.run/fal-ai/kling-video/video-to-audio`
**Endpoint ID:** `fal-ai/kling-video/video-to-audio`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/video-to-audio/playground)
>
> Run this model interactively with your own prompts.

## Quick Start

```python title="Python" theme={null}
import fal_client

def on_queue_update(update):
    if isinstance(update, fal_client.InProgress):
        for log in update.logs:
           print(log["message"])

result = fal_client.subscribe(
    "fal-ai/kling-video/video-to-audio",
    arguments={
        "video_url": "https://storage.googleapis.com/falserverless/model_tests/kling/kling-v2.5-turbo-pro-image-to-video-output.mp4"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/kling-video/video-to-audio", {
  input: {
      video_url: "https://storage.googleapis.com/falserverless/model_tests/kling/kling-v2.5-turbo-pro-image-to-video-output.mp4"
    },
  logs: true,
  onQueueUpdate: (update) => {
    if (update.status === "IN_PROGRESS") {
      update.logs.map((log) => log.message).forEach(console.log);
    }
  },
});
console.log(result.data);
console.log(result.requestId);
```

```bash title="cURL" theme={null}
curl --request POST \
  --url https://fal.run/fal-ai/kling-video/video-to-audio \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "video_url": "https://storage.googleapis.com/falserverless/model_tests/kling/kling-v2.5-turbo-pro-image-to-video-output.mp4"
}'
```

## Examples

<video src="https://v3b.fal.media/files/b/zebra/SrjZVk4yqjXSLWOdq7p6P_output.mp4" controls width="100%" />

## Capabilities

* Video input

## API Reference

### Input Schema

**video\_url** `string` *required*

The video URL to extract audio from. Only .mp4/.mov formats are supported. File size does not exceed 100MB. Video duration between 3.0s and 20.0s.

**sound\_effect\_prompt** `string` (default: Car tires screech as they accelerate in a drag race)

Sound effect prompt. Cannot exceed 200 characters. Default value: `"Car tires screech as they accelerate in a drag race"`

**background\_music\_prompt** `string` (default: intense car race)

Background music prompt. Cannot exceed 200 characters. Default value: `"intense car race"`

**asmr\_mode** `boolean` (default: false)

Enable ASMR mode. This mode enhances detailed sound effects and is suitable for highly immersive content scenarios.

### Output Schema

**video** `File` *required*

The original video with dubbed audio applied

**audio** `File` *required*

The extracted/generated audio from the video in MP3 format

## Input Example

```json theme={null}
{
  "video_url": "https://storage.googleapis.com/falserverless/model_tests/kling/kling-v2.5-turbo-pro-image-to-video-output.mp4",
  "sound_effect_prompt": "Car tires screech as they accelerate in a drag race",
  "background_music_prompt": "intense car race",
  "asmr_mode": false
}
```

## Output Example

```json theme={null}
{
  "video": {
    "url": "https://v3.fal.media/files/monkey/O-ekVTtYqeDblD1oSf2uv_dubbed_video.mp4"
  },
  "audio": {
    "url": "https://v3.fal.media/files/monkey/O-ekVTtYqeDblD1oSf2uv_extracted_audio.mp3"
  }
}
```
