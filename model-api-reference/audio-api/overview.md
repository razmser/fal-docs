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

# Audio API

> Audio API reference. Models for audio processing including text-to-speech, speech-to-text, voice cloning, and music generation..

## Overview

Models for audio processing including text-to-speech, speech-to-text, voice cloning, and music generation.

## Top Models

> [card] [Whisper API](whisper.md)
>
> Whisper is a model for speech transcription and translation.
>
> ![Example output from Whisper](https://storage.googleapis.com/falserverless/gallery/whisper.jpg)

> [card] [Kling Video Create Voice API](kling-video-create-voice.md)
>
> Create Voices to be used with Kling Models Voice Control
>
> ![Example output from Kling Video Create Voice](https://v3b.fal.media/files/b/0a868c52/jCucNmpNTg7UQ2WYGJHAr_36ad5b9439824f5e9f8fd7f1ed297d27.jpg)

> [card] [xAI Text to Speech API](xai-tts.md)
>
> Generate speech with expressive and realistic voices from xAI
>
> ![Example output from xAI Text to Speech](https://v3b.fal.media/files/b/0a92763c/4toOv_gM9xUL_Zwmt4Gc8_67e9f077fc4c48e78dfa428f7d50b190.jpg)

Explore all audio models on [fal.ai/models](https://fal.ai/models).

## Quick Start

Get started with **Whisper**:

```python title="Python" theme={null}
import fal_client

def on_queue_update(update):
    if isinstance(update, fal_client.InProgress):
        for log in update.logs:
           print(log["message"])

result = fal_client.subscribe(
    "fal-ai/whisper",
    arguments={
        "audio_url": "https://storage.googleapis.com/falserverless/model_tests/whisper/dinner_conversation.mp3"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/whisper", {
  input: {
      audio_url: "https://storage.googleapis.com/falserverless/model_tests/whisper/dinner_conversation.mp3"
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

## Pricing

For detailed pricing information, see the [fal.ai pricing page](https://fal.ai/pricing) or individual model pages.
