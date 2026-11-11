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

# Generate Videos from Image Tutorial

> Generate video clips from an image with the fal JavaScript client. Call MiniMax Video with fal.subscribe, or use Luma Dream Machine or Kling.

## How to Generate Videos using the fal API

fal offers a simple and easy-to-use API that allows you to generate videos from your images using pre-trained models. This endpoint is perfect for creating video clips from your images for various use cases such as social media, marketing, and more.

Here is an example of how to generate videos using the fal API:

```js theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/minimax-video/image-to-video", {
  input: {
    prompt: "A stylish woman walks down a Tokyo street filled with warm glowing neon and animated city signage.",
    image_url: "https://fal.media/files/elephant/8kkhB12hEZI2kkbU8pZPA_test.jpeg"
  },
});
```

## How to select the model to use

fal offers a variety of video generation models. You can select the model that best fits your needs based on the style and quality of the images you want to generate. Here are some of the available models:

* [fal-ai/minimax-video](https://fal.ai/models/fal-ai/minimax-video/image-to-video): Generate video clips from your images using MiniMax Video model.
* [fal-ai/luma-dream-machine](https://fal.ai/models/fal-ai/luma-dream-machine/image-to-video): Generate video clips from your images using Luma Dream Machine v1.5
* [fal-ai/kling-video/v1/standard](https://fal.ai/models/fal-ai/kling-video/v1/standard/image-to-video): Generate video clips from your images using Kling 1.0

To select a model, simply specify the model ID in the subscribe method as shown in the example above. You can find more models and their descriptions in the [Image to Video Models](https://fal.ai/models?categories=image-to-video) page.
