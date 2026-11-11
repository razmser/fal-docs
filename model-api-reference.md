> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](documentation/quickstart.md) and the [Model APIs overview](documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Model API Reference

> Complete API reference for fal.ai's image, video, audio, vision, and 3D generation models on fal.ai.

API reference for top models on fal.ai — complete input/output schemas, code examples in Python, JavaScript, and cURL, plus real prompt-and-output examples.

Whether you're generating images with FLUX or Nano Banana, creating videos with Seedance or Kling, synthesizing audio with ElevenLabs, or building 3D assets — find the full API documentation for each endpoint below.

> [card] [Explore All Models](https://fal.ai/models)
>
> Browse and try all models interactively on fal.ai

## Models by Category

> [card] [Video Generation API](model-api-reference/video-generation-api/overview.md)
>
> Generate videos from text prompts or images using cutting-edge video generation models. 59 models documented.
>
> ![Video Generation example](https://v3b.fal.media/files/b/0a9460e7/626QXDcTbiOI5I3LPidrR_1ae86d7b0fbb47b3b6d1a41e7fc00956.jpg)

> [card] [Image Generation API](model-api-reference/image-generation-api/overview.md)
>
> Generate and edit images using state-of-the-art diffusion and transformer models. 59 models documented.
>
> ![Image Generation example](https://v3b.fal.media/files/b/0a900b9e/ptbZcVWIQ_fXGGHfv8Zez_0ea5ca41bdf143a29e21e30a53120672.jpg)

> [card] [Audio API](model-api-reference/audio-api/overview.md)
>
> Models for audio processing including text-to-speech, speech-to-text, voice cloning, and music generation.. 5 models documented.
>
> ![Audio example](https://storage.googleapis.com/falserverless/gallery/whisper.jpg)

> [card] [Vision API](model-api-reference/vision-api/overview.md)
>
> Models for understanding and analyzing images, including captioning, visual question answering, and object detection.. 3 models documented.
>
> ![Vision example](https://v3b.fal.media/files/b/penguin/v-wl5CGbHxNVatcGXntIY_e14c7922d88348769a90469d1c206501.jpg)

> [card] [3D API](model-api-reference/3d-api/overview.md)
>
> Generate 3D assets from text or images using AI models. 6 models documented.
>
> ![3D example](https://v3b.fal.media/files/b/0a87573c/DwQCx7YBl36dnFSHaU8OT_bfc26b64d210462489530b5d5d5d98e1.jpg)

## Getting Started

New to fal.ai? Check out these resources:

* [Model APIs Overview](documentation/model-apis/overview.md) — How to call model APIs
* [Authentication](https://fal.ai/docs/documentation/authentication) — Set up your API key
* [Explore Models](https://fal.ai/models) — Browse and try models interactively
* [fal.ai Pricing](https://fal.ai/pricing) — Pricing details for all models
