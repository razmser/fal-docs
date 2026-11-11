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

# Video Generation API

> Video Generation API reference. Generate videos from text prompts or images using cutting-edge video generation models.

## Overview

Generate videos from text prompts or images using cutting-edge video generation models. These models support text-to-video, image-to-video, and video-to-video workflows.

## Top Models

> [card] [Seedance 2.0 Text to Video API API](bytedance-seedance-2.0-text-to-video.md)
>
> ByteDance's most advanced text-to-video model. Cinematic output with native audio, multi-shot editing, real-world physics, and director-level camera control.
>
> ![Example output from Seedance 2.0 Text to Video API](https://v3b.fal.media/files/b/0a9460e7/626QXDcTbiOI5I3LPidrR_1ae86d7b0fbb47b3b6d1a41e7fc00956.jpg)

> [card] [Kling Video v3 Text to Video \[Pro\] API](kling-video-v3-pro.md)
>
> Kling 3.0 Pro: Top-tier text-to-video with cinematic visuals, fluid motion, and native audio generation, with multi-shot support.
>
> ![Example output from Kling Video v3 Text to Video \[Pro\]](https://v3b.fal.media/files/b/0a8cfd13/t6TSkWzl6cFAzvO1PCdDu_f38263f637d245929f03881454951540.jpg)

> [card] [Sora 2 API](sora-2-text-to-video.md)
>
> Text-to-video endpoint for Sora 2, OpenAI's state-of-the-art video model capable of creating richly detailed, dynamic clips with audio from natural language or images.
>
> ![Example output from Sora 2](https://v3.fal.media/files/penguin/eOGowiQKXIKwyDfwgeWQO_b80784431c524553a564ebdd7550d7e6.jpg)

> [card] [Veo 3.1 API](veo3.1.md)
>
> Veo 3.1 by Google, the most advanced AI video generation model in the world. With sound on!
>
> ![Example output from Veo 3.1](https://v3b.fal.media/files/b/tiger/TGTZBJyLk9HdwjB6SB90e.png)

> [card] [Grok Imagine Video API](xai-grok-imagine-video.md)
>
> Generate videos from images with audio using xAI's Grok Imagine Video model.
>
> ![Example output from Grok Imagine Video](https://v3b.fal.media/files/b/0a8a0dd0/3wXg362MQ0pczC2ZQFBfW_c153dd469fd14b75bbd9fa6d27f1b63e.jpg)

> [card] [Topaz Video Upscale API](topaz-upscale.md)
>
> Professional-grade video upscaling using Topaz technology. Enhance your videos with high-quality upscaling.
>
> ![Example output from Topaz Video Upscale](https://storage.googleapis.com/falserverless/gallery/topaz-video-upscale.png)

> [card] [SeedVR2 API](seedvr-upscale.md)
>
> Upscale your videos using SeedVR2 with temporal consistency!
>
> ![Example output from SeedVR2](https://v3.fal.media/files/koala/cAs3xUC9Lx-jlQh-27-60_4dddbb5a9bac42da86ddf20a5b9d30b3.jpg)

> [card] [Video API](bria-video.md)
>
> Automatically remove backgrounds from videos -perfect for creating clean, professional content without a green screen.
>
> ![Example output from Video](https://storage.googleapis.com/fal_cdn/fal/Sound-2.jpg)

> [card] [Birefnet API](birefnet-v2.md)
>
> Video background removal version of bilateral reference framework (BiRefNet) for high-resolution dichotomous image segmentation (DIS)
>
> ![Example output from Birefnet](https://v3b.fal.media/files/b/monkey/SKrLsOM3RMOk-QuNj8-io_6356be1b301e454394d2e0a853b98c76.jpg)

Explore all video generation models on [fal.ai/models](https://fal.ai/models).

## Quick Start

Get started with **Bytedance Seedance 2.0 Text To Video**:

```python title="Python" theme={null}
import fal_client

def on_queue_update(update):
    if isinstance(update, fal_client.InProgress):
        for log in update.logs:
           print(log["message"])

result = fal_client.subscribe(
    "bytedance/seedance-2.0/text-to-video",
    arguments={
        "prompt": "An octopus finds a football in the ocean and excitedly calls its octopus friends to come and play. Cut scene to an octopus football game under the sea."
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("bytedance/seedance-2.0/text-to-video", {
  input: {
      prompt: "An octopus finds a football in the ocean and excitedly calls its octopus friends to come and play. Cut scene to an octopus football game under the sea."
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
