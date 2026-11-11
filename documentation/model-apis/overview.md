> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Model APIs: Generate Images, Video, and Audio

> Call 1,000+ image, video, audio, and multimodal models through one API. Install the Python or JavaScript client, set FAL_KEY, and run your first model.

Model APIs gives you instant access to state-of-the-art AI models for image, video, audio, and multimodal generation. Every model is already optimized and production-ready, so you can [authenticate](https://fal.ai/docs/documentation/model-apis/authentication.md) and start generating immediately.

Each model runs on fal's infrastructure with automatic scaling, [queue-based reliability](inference/queue.md), and [pay-per-use billing](pricing.md). You call them the same way whether you use the [Python or JavaScript client](inference/client-setup.md) or [raw HTTP](../../api-reference/inference-api.md). If you need to deploy your own model instead, see [Serverless](../serverless.md). For an overview of how Model APIs and Serverless work together, see the [fal inference platform](https://fal.ai/inference).

## Quick Example

Generate an image in three lines of code. Install the client, set your [API key](https://fal.ai/docs/documentation/model-apis/authentication.md), and call a model.

```python Python theme={null}
import fal_client

result = fal_client.subscribe("fal-ai/nano-banana-2", arguments={
    "prompt": "a futuristic cityscape at sunset"
})
print(result["images"][0]["url"])
```

```javascript JavaScript theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a futuristic cityscape at sunset" }
});
console.log(result.data.images[0].url);
```

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/fal-ai/nano-banana-2" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a futuristic cityscape at sunset"}'
```

The response includes a [CDN URL](fal-cdn.md) for the generated image, along with metadata like dimensions and seed. Every model follows the same pattern: send inputs as JSON, receive outputs as JSON with media URLs.

## How It Works

Every model on fal is exposed as an HTTP endpoint, documented in the [Inference API reference](../../api-reference/inference-api.md). You can call it directly, or go through the [queue](inference/queue.md) for automatic retries, status tracking, and scaling. There are several calling patterns depending on your use case.

**[Direct (`run`)](inference/synchronous.md)** sends a synchronous HTTP request to `fal.run` and returns the result directly. This is the simplest approach for quick scripts and prototyping.

**[Subscribe](inference/synchronous.md)** uses the queue under the hood but handles polling automatically, so it feels synchronous. This is what the Quick Example above uses.

**[Asynchronous (`submit`)](inference/queue.md)** gives you full control over the queue. Submit a request and return immediately, then poll for status or receive results via [webhook](inference/webhooks.md). This is the recommended approach for production workloads with parallel processing.

**[Streaming](inference/streaming.md)** delivers output progressively as the model generates it. This is useful for LLMs that produce tokens incrementally, or for showing generation progress in a UI.

**[`realtime()`](inference/real-time.md)** uses WebSockets for persistent connections, bypassing the queue entirely for sub-100ms latency. Only available for models with an explicit real-time endpoint.

## What You Can Generate

The [model gallery](https://fal.ai/models) has 1,000+ models spanning several categories. Here are some popular starting points.

### Image Generation and Editing

> [card] [Nano Banana 2](https://fal.ai/models/fal-ai/nano-banana-2)
>
> Google's fast image generation and editing model

> [card] [Nano Banana Pro](https://fal.ai/models/fal-ai/nano-banana-pro)
>
> State-of-the-art image generation with realism and typography

> [card] [Flux 2 Flex](https://fal.ai/models/fal-ai/flux-2-flex)
>
> Enhanced typography and text rendering from BFL

> [card] [Recraft V4 Pro](https://fal.ai/models/fal-ai/recraft/v4/pro/text-to-image)
>
> Professional design and marketing visuals

> [card] [Nano Banana 2 Edit](https://fal.ai/models/fal-ai/nano-banana-2/edit)
>
> Intelligent image editing with Google's latest model

> [card] [FLUX Kontext Pro](https://fal.ai/models/fal-ai/flux-pro/kontext)
>
> Targeted edits and complex scene transformations

### Video Generation

> [card] [Kling 3.0 Pro](https://fal.ai/models/fal-ai/kling-video/v2.5-turbo/pro/image-to-video)
>
> Cinematic image-to-video with fluid motion

> [card] [Kling O3](https://fal.ai/models/fal-ai/kling-video/o3/standard/image-to-video)
>
> Start and end frame animation with scene guidance

> [card] [LTX-2 19B](https://fal.ai/models/fal-ai/ltx-2-19b/image-to-video)
>
> Video with audio from images using LTX-2

> [card] [FLUX 3 Max](https://fal.ai/models/blackforestlabs/flux-3-max/text-to-video)
>
> Text-to-video with optional audio, post-trained by fal with Black Forest Labs

> [card] [MiniMax H3 Max](https://fal.ai/models/minimax/h3-max/text-to-video)
>
> Video with synchronized audio, post-trained by fal from MiniMax H3

### Audio and Speech

> [card] [Chatterbox TTS](https://fal.ai/models/fal-ai/chatterbox/text-to-speech)
>
> Natural text-to-speech from Resemble AI

> [card] [MiniMax Speech-02 HD](https://fal.ai/models/fal-ai/minimax/speech-02-hd)
>
> High-quality multi-voice text-to-speech

> [card] [Dia TTS](https://fal.ai/models/fal-ai/dia-tts/voice-clone)
>
> Multi-speaker dialogue with voice cloning

> [card] [Beatoven Music](https://fal.ai/models/fal-ai/beatoven/music-generation)
>
> Royalty-free instrumental music generation

> [card] [Beatoven SFX](https://fal.ai/models/fal-ai/beatoven/sound-effect-generation)
>
> Professional sound effect generation

> [card] [ElevenLabs Music](https://fal.ai/models/fal-ai/elevenlabs/music)
>
> High quality, realistic music generation

> [card] [Explore All Models](https://fal.ai/explore)
>
> Browse 1,000+ models across image, video, audio, 3D, and more

Every model page on fal.ai includes a [Playground](playground.md) for testing, full API documentation with [input/output schemas](common-parameters.md), [pricing](pricing.md), and ready-to-copy code examples.

## Next Steps

> [card] [Playground](playground.md)
>
> Test and compare models interactively before integrating

> [card] [Inference](inference.md)
>
> Learn the different ways to call models

> [card] [Client Setup](inference/client-setup.md)
>
> Install and configure the fal client for Python, JavaScript, and more

> [card] [Examples](../../examples.md)
>
> Step-by-step tutorials for image, video, and audio generation
