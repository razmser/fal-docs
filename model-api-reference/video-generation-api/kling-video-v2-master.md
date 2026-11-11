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

# Kling Video V2 Master API

> API reference for Kling Video V2 Master. Generate video clips from your images using Kling 2.0 Master

> [tab] Image To Video
>
> **Endpoint:** `POST https://fal.run/fal-ai/kling-video/v2/master/image-to-video`
> **Endpoint ID:** `fal-ai/kling-video/v2/master/image-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/v2/master/image-to-video/playground)
> >
> > Run this model interactively with your own prompts.
>
> ### Quick Start
>
> ```python title="Python" theme={null}
> import fal_client
>
> def on_queue_update(update):
>     if isinstance(update, fal_client.InProgress):
>         for log in update.logs:
>            print(log["message"])
>
> result = fal_client.subscribe(
>     "fal-ai/kling-video/v2/master/image-to-video",
>     arguments={
>         "prompt": "slow-motion sequence captures the catastrophic implosion of a skyscraper, dust and debris billowing outwards in a chaotic ballet of destruction, while a haunting, orchestral score underscores the sheer power and finality of the event.",
>         "image_url": "https://v3.fal.media/files/elephant/rkH-9qoXtXu3rAYTsx9V5_image.webp"
>     },
>     with_logs=True,
>     on_queue_update=on_queue_update,
> )
> print(result)
> ```
>
> ```javascript title="JavaScript" theme={null}
> import { fal } from "@fal-ai/client";
>
> const result = await fal.subscribe("fal-ai/kling-video/v2/master/image-to-video", {
>   input: {
>       prompt: "slow-motion sequence captures the catastrophic implosion of a skyscraper, dust and debris billowing outwards in a chaotic ballet of destruction, while a haunting, orchestral score underscores the sheer power and finality of the event.",
>       image_url: "https://v3.fal.media/files/elephant/rkH-9qoXtXu3rAYTsx9V5_image.webp"
>     },
>   logs: true,
>   onQueueUpdate: (update) => {
>     if (update.status === "IN_PROGRESS") {
>       update.logs.map((log) => log.message).forEach(console.log);
>     }
>   },
> });
> console.log(result.data);
> console.log(result.requestId);
> ```
>
> ```bash title="cURL" theme={null}
> curl --request POST \
>   --url https://fal.run/fal-ai/kling-video/v2/master/image-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "slow-motion sequence captures the catastrophic implosion of a skyscraper, dust and debris billowing outwards in a chaotic ballet of destruction, while a haunting, orchestral score underscores the sheer power and finality of the event.",
>   "image_url": "https://v3.fal.media/files/elephant/rkH-9qoXtXu3rAYTsx9V5_image.webp"
> }'
> ```
>
> ## Examples
>
> > A close-up reveals a woman's delicate fingers gracefully holding a perfume bottle, its crystal facets catching the light, a slow, deliberate movement emphasizing the product's luxurious quality;  the scene is bathed in soft, warm lighting, creating an intimate and sensual atmosphere. Camera zoom out...
>
> <video src="https://v3.fal.media/files/zebra/vvzj5u7wv9wk06GA4zTJX_output.mp4" controls width="100%" />
>
> > Under a vast, clear sky, a Shub-Niggurath shepherd in futuristic soldier gear and a shaved-sides haircut smiles and laughs faintly while casually posing near a cybernetics clinic set against a sandy, rocky landscape, a relaxed picnic spread laid out nearby.
>
> <video src="https://v3.fal.media/files/kangaroo/IkMxgPpvf3jZ5UKfrlnEY_output.mp4" controls width="100%" />
>
> > A warm, golden light bathes a cozy bedroom where a fluffy Persian cat and a scruffy terrier sleep curled together on a plush bed, their gentle breaths creating a peaceful atmosphere; the scene unfolds in a slow, deliberate pace with the camera maintaining a steady position, focusing on the subtle mo...
>
> <video src="https://v3.fal.media/files/lion/DDHlO3zS6d9QvQTZqC6L0_output.mp4" controls width="100%" />
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> **image\_url** `string` *required*
>
> URL of the image to be used for the video
>
> **duration** `DurationEnum` (default: 5)
>
> The duration of the generated video in seconds Default value: `"5"`
>
> Possible values: `5`, `10`
>
> **negative\_prompt** `string` (default: blur, distort, and low quality)
>
> Default value: `"blur, distort, and low quality"`
>
> **cfg\_scale** `float` (default: 0.5)
>
> The CFG (Classifier Free Guidance) scale is a measure of how close you want
> the model to stick to your prompt. Default value: `0.5`
>
> Range: `0` to `1`
>
> ### Output Schema
>
> **video** `File` *required*
>
> The generated video
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "slow-motion sequence captures the catastrophic implosion of a skyscraper, dust and debris billowing outwards in a chaotic ballet of destruction, while a haunting, orchestral score underscores the sheer power and finality of the event.",
>   "image_url": "https://v3.fal.media/files/elephant/rkH-9qoXtXu3rAYTsx9V5_image.webp",
>   "duration": "5",
>   "negative_prompt": "blur, distort, and low quality",
>   "cfg_scale": 0.5
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "url": "https://v3.fal.media/files/koala/VvGXP5xEhTR9ovGjpulJ7_output.mp4"
>   }
> }
> ```

> [tab] Text To Video
>
> **Endpoint:** `POST https://fal.run/fal-ai/kling-video/v2/master/text-to-video`
> **Endpoint ID:** `fal-ai/kling-video/v2/master/text-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/v2/master/text-to-video/playground)
> >
> > Run this model interactively with your own prompts.
>
> ### Quick Start
>
> ```python title="Python" theme={null}
> import fal_client
>
> def on_queue_update(update):
>     if isinstance(update, fal_client.InProgress):
>         for log in update.logs:
>            print(log["message"])
>
> result = fal_client.subscribe(
>     "fal-ai/kling-video/v2/master/text-to-video",
>     arguments={
>         "prompt": "A slow-motion drone shot descending from above a maze of neon-lit Tokyo alleyways at night during heavy rainfall. The camera gradually focuses on a lone figure in a luminescent white raincoat standing perfectly still amid the bustling crowd, all carrying black umbrellas. As the camera continues its downward journey, we see the raindrops creating rippling patterns on puddles that reflect the kaleidoscope of colors from the surrounding signs, creating a mirror world beneath the city."
>     },
>     with_logs=True,
>     on_queue_update=on_queue_update,
> )
> print(result)
> ```
>
> ```javascript title="JavaScript" theme={null}
> import { fal } from "@fal-ai/client";
>
> const result = await fal.subscribe("fal-ai/kling-video/v2/master/text-to-video", {
>   input: {
>       prompt: "A slow-motion drone shot descending from above a maze of neon-lit Tokyo alleyways at night during heavy rainfall. The camera gradually focuses on a lone figure in a luminescent white raincoat standing perfectly still amid the bustling crowd, all carrying black umbrellas. As the camera continues its downward journey, we see the raindrops creating rippling patterns on puddles that reflect the kaleidoscope of colors from the surrounding signs, creating a mirror world beneath the city."
>     },
>   logs: true,
>   onQueueUpdate: (update) => {
>     if (update.status === "IN_PROGRESS") {
>       update.logs.map((log) => log.message).forEach(console.log);
>     }
>   },
> });
> console.log(result.data);
> console.log(result.requestId);
> ```
>
> ```bash title="cURL" theme={null}
> curl --request POST \
>   --url https://fal.run/fal-ai/kling-video/v2/master/text-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A slow-motion drone shot descending from above a maze of neon-lit Tokyo alleyways at night during heavy rainfall. The camera gradually focuses on a lone figure in a luminescent white raincoat standing perfectly still amid the bustling crowd, all carrying black umbrellas. As the camera continues its downward journey, we see the raindrops creating rippling patterns on puddles that reflect the kaleidoscope of colors from the surrounding signs, creating a mirror world beneath the city."
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> **duration** `DurationEnum` (default: 5)
>
> The duration of the generated video in seconds Default value: `"5"`
>
> Possible values: `5`, `10`
>
> **aspect\_ratio** `AspectRatioEnum` (default: 16:9)
>
> The aspect ratio of the generated video frame Default value: `"16:9"`
>
> Possible values: `16:9`, `9:16`, `1:1`
>
> **negative\_prompt** `string` (default: blur, distort, and low quality)
>
> Default value: `"blur, distort, and low quality"`
>
> **cfg\_scale** `float` (default: 0.5)
>
> The CFG (Classifier Free Guidance) scale is a measure of how close you want
> the model to stick to your prompt. Default value: `0.5`
>
> Range: `0` to `1`
>
> ### Output Schema
>
> **video** `File` *required*
>
> The generated video
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "A slow-motion drone shot descending from above a maze of neon-lit Tokyo alleyways at night during heavy rainfall. The camera gradually focuses on a lone figure in a luminescent white raincoat standing perfectly still amid the bustling crowd, all carrying black umbrellas. As the camera continues its downward journey, we see the raindrops creating rippling patterns on puddles that reflect the kaleidoscope of colors from the surrounding signs, creating a mirror world beneath the city.",
>   "duration": "5",
>   "aspect_ratio": "16:9",
>   "negative_prompt": "blur, distort, and low quality",
>   "cfg_scale": 0.5
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "url": "https://v3.fal.media/files/rabbit/5fu6OSZdvV825r2s_c0S8_output.mp4"
>   }
> }
> ```

## Kling AI v2 Master - Professional Image-to-Video Generation

Transform static images into dynamic, fluid video content with Kling AI v2 Master. Built by Kuaishou and optimized for professional creative workflows, this advanced AI model generates high-quality video animations from single images with unprecedented control and natural motion.

### Overview

Kling AI v2 Master excels at creating smooth, realistic video animations from still images. Whether you're a content creator, marketing professional, or developer building video applications, this model delivers production-ready results through an intuitive API. As the premium tier offering, it provides superior motion fluidity and cinematic quality for professional-grade video creation.

#### Key Capabilities

Transform any still image into fluid video content with:

* Natural motion patterns that respect physics and object relationships
* Precise control over animation duration (5 or 10 seconds)
* Consistent quality across diverse image types
* Professional-grade cinematic output
* Enhanced visual aesthetics and motion dynamics

### Getting Started

Getting up and running with Kling AI is straightforward. First, install your preferred SDK:

JavaScript/TypeScript:

```bash theme={null}
npm install --save @fal-ai/client
```

Python:

```bash theme={null}
pip install fal-client
```

#### Authentication

Configure your API credentials:

```typescript theme={null}
import { fal } from "@fal-ai/client";

fal.config({
  credentials: "YOUR_FAL_KEY_HERE"
});
```

#### Basic Usage

Here's a simple example of generating a video from an image:

```typescript theme={null}
const result = await fal.subscribe("fal-ai/kling-video/v2/master/image-to-video", {
  input: {
    prompt: "slow-motion sequence captures the catastrophic implosion of a skyscraper",
    image_url: "https://example.com/source-image.jpg",
    duration: "5",
    aspect_ratio: "16:9",
    negative_prompt: "blur, distort, and low quality",
    cfg_scale: 0.5
  }
});

console.log(result.video.url);
```

### API Parameters

* `prompt` (required): Text description of the desired motion and scene
* `image_url` (required): URL of the input image
* `duration`: Video duration - "5" or "10" seconds
* `aspect_ratio`: Output aspect ratio (e.g., "16:9", "9:16", "1:1")
* `negative_prompt`: Elements to avoid in the generation
* `cfg_scale`: Classifier Free Guidance scale (default: 0.5)
* `static_mask_url`: URL for static motion brush mask
* `dynamic_mask_url`: URL for dynamic motion brush mask
* `special_fx`: Special effects options
* `input_image_urls`: Array of up to 4 images for multi-image generation

### Advanced Features

The v2 Master model provides sophisticated controls for professional applications:

#### Motion Control

Adjust motion intensity and patterns through intuitive parameters. Fine-tune the balance between dramatic movement and natural flow to match your creative vision using cfg\_scale and motion masks.

#### Quality Settings

Master tier delivers the highest quality output with enhanced detail and smoother motion, ideal for professional productions and cinematic content.

#### Format Options

Export to multiple video formats with control over resolution and aspect ratio to optimize for different distribution channels.

### Queue Management

For asynchronous processing:

```typescript theme={null}
// Submit request
const { request_id } = await fal.queue.submit("fal-ai/kling-video/v2/master/image-to-video", {
  input: {
    prompt: "Your cinematic description",
    image_url: "your-image-url",
    duration: "10"
  }
});

// Check status
const status = await fal.queue.status("fal-ai/kling-video/v2/master/image-to-video", {
  requestId: request_id,
  logs: true
});

// Get result
const result = await fal.queue.result("fal-ai/kling-video/v2/master/image-to-video", {
  requestId: request_id
});
```

### Best Practices

For optimal results:

Start with high-quality source images. The model performs best with clear, well-lit images at high resolution.

Test different cfg\_scale values. Begin with moderate settings (0.5) and adjust based on your content needs.

Consider your target platform. Adjust aspect ratio and duration settings to match your distribution requirements while maintaining quality.

### Technical Support

Our comprehensive documentation covers advanced usage scenarios, parameter tuning, and troubleshooting. For additional support:

* Visit our documentation portal for detailed guides
* Join our developer community for peer support
* Contact our technical team for enterprise assistance

### Pricing and Usage

Premium pricing for professional quality:

* **5-second video**: \$1.40
* **Additional seconds**: \$0.28 per second
* 10-second video: \$2.80

[View detailed pricing](https://fal.ai/pricing) or [contact sales](mailto:support@fal.ai) for enterprise solutions.

### Model Variants

Kling offers multiple quality tiers:

* **Master**: This model - Premium quality for professional use
* **Standard**: `fal-ai/kling-video/v2/standard/image-to-video` - Cost-efficient option
* **v2.1 Versions**: Latest iteration with additional improvements

### Support and Resources

* API Documentation: [fal.ai/models/fal-ai/kling-video/v2/master/image-to-video/api](https://fal.ai/models/fal-ai/kling-video/v2/master/image-to-video/api)
* Discord Community: [discord.com/invite/fal-ai](https://discord.com/invite/fal-ai)
* Support: [support@fal.ai](mailto:support@fal.ai)

### Ready to Create Cinematic Videos?

Get started with Kling AI v2 Master today and experience professional-grade video generation capabilities. Create your API key at [fal.ai](https://fal.ai) and transform your images into stunning, cinematic videos.

## Related

* [Kling 2.0 Master](https://fal.ai/docs/model-api-reference/video-generation-api/kling-2.0-master) — Video Generation

## Limitations

* `duration` restricted to: `5`, `10`
* `cfg_scale` range: 0 to 1
* `aspect_ratio` restricted to: `16:9`, `9:16`, `1:1`
