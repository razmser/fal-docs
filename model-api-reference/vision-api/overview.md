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

# Vision API

> Vision API reference. Models for understanding and analyzing images, including captioning, visual question answering, and object detection..

## Overview

Models for understanding and analyzing images, including captioning, visual question answering, and object detection.

## Top Models

> [card] [OpenRouter \[Vision\] API](openrouter-router.md)
>
> Run any Vision Language Model with fal. Analyze and understand images using Claude (Anthropic), GPT-5 / GPT-4o (OpenAI), Gemini (Google), Grok (xAI), Llama (Meta), Qwen, Pixtral (Mistral), and more. S
>
> ![Example output from OpenRouter \[Vision\]](https://v3b.fal.media/files/b/penguin/v-wl5CGbHxNVatcGXntIY_e14c7922d88348769a90469d1c206501.jpg)

> [card] [NSFW Filter API](imageutils.md)
>
> Predict the probability of an image being NSFW.
>
> ![Example output from NSFW Filter](https://storage.googleapis.com/falserverless/gallery/nsfw-filter.webp)

> [card] [Florence-2 Large API](florence-2-large.md)
>
> Florence-2 is an advanced vision foundation model that uses a prompt-based approach to handle a wide range of vision and vision-language tasks
>
> ![Example output from Florence-2 Large](https://storage.googleapis.com/falserverless/gallery/florence-2-large.jpeg)

Explore all vision models on [fal.ai/models](https://fal.ai/models).

## Quick Start

Get started with **OpenRouter \[Vision]**:

```python title="Python" theme={null}
import fal_client

def on_queue_update(update):
    if isinstance(update, fal_client.InProgress):
        for log in update.logs:
           print(log["message"])

result = fal_client.subscribe(
    "openrouter/router/vision",
    arguments={
        "image_urls": [
            "https://fal.media/files/tiger/4Ew1xYW6oZCs6STQVC7V8_86440216d0fe42e4b826d03a2121468e.jpg"
        ],
        "prompt": "Caption this image for a text-to-image model with as much detail as possible.",
        "model": "google/gemini-2.5-flash"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("openrouter/router/vision", {
  input: {
      image_urls: [
        "https://fal.media/files/tiger/4Ew1xYW6oZCs6STQVC7V8_86440216d0fe42e4b826d03a2121468e.jpg"
      ],
      prompt: "Caption this image for a text-to-image model with as much detail as possible.",
      model: "google/gemini-2.5-flash"
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
