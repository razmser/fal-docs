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

# Image Generation API

> Image Generation API reference. Generate and edit images using state-of-the-art diffusion and transformer models.

## Overview

Generate and edit images using state-of-the-art diffusion and transformer models. fal.ai offers a wide range of models for text-to-image generation, image-to-image editing, style transfer, and context-aware transformations — optimized for different use cases from fast prototyping to production-quality output.

## Top Models

> [card] [Nano Banana 2 API](nano-banana-2.md)
>
> Nano Banana 2 is Google's new state-of-the-art fast image generation and editing model
>
> ![Example output from Nano Banana 2](https://v3b.fal.media/files/b/0a900b9e/ptbZcVWIQ_fXGGHfv8Zez_0ea5ca41bdf143a29e21e30a53120672.jpg)

> [card] [Nano Banana Pro API](nano-banana-pro.md)
>
> Nano Banana Pro is Google's new state-of-the-art image generation and editing model
>
> ![Example output from Nano Banana Pro](https://v3b.fal.media/files/b/0a8691ce/SR0_u1zPJbx8jCIO6bJR0_8c83f0d66bbd48f3b55f825117941f84.jpg)

> [card] [Flux 2 Pro API](flux-2-pro.md)
>
> Image editing with FLUX.2 \[pro] from Black Forest Labs. Ideal for high-quality image manipulation, style transfer, and sequential editing workflows
>
> ![Example output from Flux 2 Pro](https://v3b.fal.media/files/b/penguin/UfryXXm9my6IM8HsoP9FL_054c2c2953dc491996904114c6e04836.jpg)

> [card] [FLUX1.1 \[pro\] ultra API](flux-pro-v1.1-ultra.md)
>
> FLUX1.1 \[pro] ultra is the newest version of FLUX1.1 \[pro], maintaining professional-grade image quality while delivering up to 2K resolution with improved photo realism.
>
> ![Example output from FLUX1.1 \[pro\] ultra](https://storage.googleapis.com/falserverless/gallery/flux-pro-v1-1-ultra.webp)

> [card] [FLUX.1 \[dev\] API](flux-dev.md)
>
> FLUX.1 \[dev] is a 12 billion parameter flow transformer that generates high-quality images from text. It is suitable for personal and commercial use.
>
> ![Example output from FLUX.1 \[dev\]](https://storage.googleapis.com/fal_cdn/fal/Upscale-1.jpeg)

> [card] [FLUX.1 \[dev\] with LoRAs API](flux-lora.md)
>
> Super fast endpoint for the FLUX.1 \[dev] model with LoRA support, enabling rapid and high-quality image generation using pre-trained LoRA adaptations for personalization, specific styles, brand identi
>
> ![Example output from FLUX.1 \[dev\] with LoRAs](https://fal.media/files/penguin/hHhmyazpcSq_W8c0TzXl4_14015fe83e3541848e6b2e2e1c7e4968.jpg)

> [card] [Nano Banana API](nano-banana.md)
>
> Google's famous original image generation and editing model
>
> ![Example output from Nano Banana](https://v3b.fal.media/files/b/0a95c085/S0ExtNZpsp_6xygCJ3MKG_Bj98CiYt.png)

> [card] [Bytedance API](bytedance-seedream-v4.5.md)
>
> A new-generation image creation model ByteDance, Seedream 4.5 integrates image generation and image editing capabilities into a single, unified architecture.
>
> ![Example output from Bytedance](https://v3b.fal.media/files/b/0a84910b/lDvWLqpxcFmzQHdadDA7t_4012f28b4f79444782d90d8927b42c29.jpg)

> [card] [Grok Imagine Image API](xai-grok-imagine-image.md)
>
> Edit images precisely with xAI's Grok Imagine model
>
> ![Example output from Grok Imagine Image](https://v3b.fal.media/files/b/0a8a0dc7/kv2XmZ5em8Kb2-7oXZndp_b897f79cbc564e1d8f4e5d5c71710c2b.jpg)

> [card] [GPT-Image 1.5 API](gpt-image-1.5.md)
>
> GPT Image 1.5 generates high-fidelity images with strong prompt adherence, preserving composition, lighting, and fine-grained detail.
>
> ![Example output from GPT-Image 1.5](https://v3b.fal.media/files/b/0a875814/3KvlvWItvogHIOuPRWIVl_001c9e77742a4ff2a829a8ece127c0d1.jpg)

> [card] [SeedVR2 API](seedvr-upscale-image.md)
>
> Use SeedVR2 to upscale your images
>
> ![Example output from SeedVR2](https://v3.fal.media/files/panda/_-1rRy0_I6w-fbt3q0RDL_2f47fece6e2b433994dda482e8d20bb9.jpg)

> [card] [Gemini 3 Pro Image Preview API](gemini-3-pro-image-preview.md)
>
> Gemini 3 Pro Image (a.k.a Nano Banana Pro) is Google's state-of-the-art high-fidelity image generation and editing model
>
> ![Example output from Gemini 3 Pro Image Preview](https://v3b.fal.media/files/b/penguin/Vp2KdBwHgd4Qp0ixnsOue_eb81afc63ac24064bc9d3e9ed48f9b74.jpg)

> [card] [Bria RMBG 2.0 API](bria-background.md)
>
> Bria RMBG 2.0 enables seamless removal of backgrounds from images, ideal for professional editing tasks. Trained exclusively on licensed data for safe and risk-free commercial use. Model weights for c
>
> ![Example output from Bria RMBG 2.0](https://v3.fal.media/files/lion/YBr6fXnaBoA_Hsy89qG6Q_44ab15cf50b0431fa4786c565c5b9032.png)

> [card] [Z-Image Turbo API](z-image-turbo.md)
>
> Z-Image Turbo is a super fast text-to-image model of 6B parameters developed by Tongyi-MAI.
>
> ![Example output from Z-Image Turbo](https://v3b.fal.media/files/b/koala/Pvji5sM6n6oNIo-p0Xsn__245f3d4eb5df4952aa80ec74f4afd7f2.jpg)

> [card] [Birefnet Background Removal API](birefnet.md)
>
> bilateral reference framework (BiRefNet) for high-resolution dichotomous image segmentation (DIS)
>
> ![Example output from Birefnet Background Removal](https://storage.googleapis.com/falserverless/gallery/birefnet.webp)

> [card] [Topaz API](topaz-upscale.md)
>
> Use the powerful and accurate topaz image enhancer to enhance your images.
>
> ![Example output from Topaz](https://storage.googleapis.com/fal_cdn/fal/Sound-3.jpg)

> [card] [Ideogram Text to Image API](ideogram-v3.md)
>
> Generate high-quality images, posters, and logos with Ideogram V3. Features exceptional typography handling and realistic outputs optimized for commercial and creative use.
>
> ![Example output from Ideogram Text to Image](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/image-5.png?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=a5eb3affecfe0e642fd722ebf728f088)

> [card] [Remove Background API](imageutils.md)
>
> Remove the background from an image.
>
> ![Example output from Remove Background](https://mintcdn.com/fal-d8505a2e/_1QeqsRe91WUAOCJ/images/image-5.png?fit=max\&auto=format\&n=_1QeqsRe91WUAOCJ\&q=85\&s=a5eb3affecfe0e642fd722ebf728f088)

> [card] [Florence-2 Large API](florence-2-large.md)
>
> Florence-2 is an advanced vision foundation model that uses a prompt-based approach to handle a wide range of vision and vision-language tasks
>
> ![Example output from Florence-2 Large](https://storage.googleapis.com/falserverless/gallery/florence-2-large.jpeg)

Explore all image generation models on [fal.ai/models](https://fal.ai/models).

## Quick Start

Get started with **Nano Banana 2**:

```python title="Python" theme={null}
import fal_client

def on_queue_update(update):
    if isinstance(update, fal_client.InProgress):
        for log in update.logs:
           print(log["message"])

result = fal_client.subscribe(
    "fal-ai/nano-banana-2",
    arguments={
        "prompt": "An action shot of a black lab swimming in an inground suburban swimming pool. The camera is placed meticulously on the water line, dividing the image in half, revealing both the dogs head above water holding a tennis ball in it's mouth, and it's paws paddling underwater."
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: {
      prompt: "An action shot of a black lab swimming in an inground suburban swimming pool. The camera is placed meticulously on the water line, dividing the image in half, revealing both the dogs head above water holding a tennis ball in it's mouth, and it's paws paddling underwater."
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
