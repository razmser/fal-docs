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

# Flux Schnell API

> API reference for Flux Schnell. FLUX.1 [schnell] is a 12 billion parameter flow transformer that generates high-quality images from text in 1 to 4 steps, suitable for personal and commercial use.

> [tab] Schnell
>
> **Endpoint:** `POST https://fal.run/fal-ai/flux/schnell`
> **Endpoint ID:** `fal-ai/flux/schnell`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/flux/schnell/playground)
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
>     "fal-ai/flux/schnell",
>     arguments={
>         "prompt": "Extreme close-up of a single tiger eye, direct frontal view. Detailed iris and pupil. Sharp focus on eye texture and color. Natural lighting to capture authentic eye shine and depth. The word \"FLUX\" is painted over it in big, white brush strokes with visible texture."
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
> const result = await fal.subscribe("fal-ai/flux/schnell", {
>   input: {
>       prompt: "Extreme close-up of a single tiger eye, direct frontal view. Detailed iris and pupil. Sharp focus on eye texture and color. Natural lighting to capture authentic eye shine and depth. The word \"FLUX\" is painted over it in big, white brush strokes with visible texture."
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
>   --url https://fal.run/fal-ai/flux/schnell \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Extreme close-up of a single tiger eye, direct frontal view. Detailed iris and pupil. Sharp focus on eye texture and color. Natural lighting to capture authentic eye shine and depth. The word \"FLUX\" is painted over it in big, white brush strokes with visible texture."
> }'
> ```
>
> ## Examples
>
> > portrait | wide angle shot of eyes off to one side of frame, lucid dream-like woman, looking off in distance ::8 style | daydreampunk with glowing skin and eyes, styled in headdress, beautiful, she is dripping in neon lights, very colorful blue, green, purple, bioluminescent, glowing ::8 background ...
>
> ![Generated image: portrait | wide angle shot of eyes off to one side of frame, lucid dream-like wo](https://fal.media/files/tiger/m0K3P3JUR_Brcf7mxk3tl.png)
>
> > Anime style portrait of a female samurai at a beautiful lake with cherry trees, mountain fuji background, spring, sunset
>
> ![Generated image: Anime style portrait of a female samurai at a beautiful lake with cherry trees, ](https://fal.media/files/panda/a7P7ZT9XQWNQJ20NP1P8S.png)
>
> > A vibrant custom illustration, retro-style, featuring a vintage ice cream cart, by the beach, with palm trees. The text 'Gelato' is displayed in a retro, distressed style, with each letter in a different color, exuding a sense of nostalgia. The design is perfect for a t-shirt print, with an isolated...
>
> ![Generated image: A vibrant custom illustration, retro-style, featuring a vintage ice cream cart, ](https://fal.media/files/lion/aO029csoRCHt4RHWKn0UU.png)
>
> ### Input Schema
>
> **num\_inference\_steps** `integer` (default: 4)
>
> The number of inference steps to perform. Default value: `4`
>
> Range: `1` to `12`
>
> **prompt** `string` *required*
>
> The prompt to generate an image from.
>
> **image\_size** `ImageSize | Enum` (default: landscape\_4\_3)
>
> The size of the generated image. Default value: `landscape_4_3`
>
> Possible values: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`
>
> **seed** `integer`
>
> The same seed and the same prompt given to the same version of the model
> will output the same image every time.
>
> **guidance\_scale** `float` (default: 3.5)
>
> The CFG (Classifier Free Guidance) scale is a measure of how close you want
> the model to stick to your prompt when looking for a related image to show you. Default value: `3.5`
>
> Range: `1` to `20`
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **num\_images** `integer` (default: 1)
>
> The number of images to generate. Default value: `1`
>
> Range: `1` to `4`
>
> **enable\_safety\_checker** `boolean` (default: true)
>
> If set to true, the safety checker will be enabled. Default value: `true`
>
> **output\_format** `OutputFormatEnum` (default: jpeg)
>
> The format of the generated image. Default value: `"jpeg"`
>
> Possible values: `jpeg`, `png`
>
> **acceleration** `AccelerationEnum` (default: none)
>
> The speed of the generation. The higher the speed, the faster the generation. Default value: `"none"`
>
> Possible values: `none`, `regular`, `high`
>
> ### Output Schema
>
> **images** `list\<Image\>` *required*
>
> The generated image files info.
>
> **timings** `Timings` *required*
>
> **seed** `integer` *required*
>
> Seed of the generated Image. It will be the same value of the one passed in the
> input or the randomly generated that was used in case none was passed.
>
> **has\_nsfw\_concepts** `list\<boolean\>` *required*
>
> Whether the generated images contain NSFW concepts.
>
> **prompt** `string` *required*
>
> The prompt used for generating the image.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "num_inference_steps": 4,
>   "prompt": "Extreme close-up of a single tiger eye, direct frontal view. Detailed iris and pupil. Sharp focus on eye texture and color. Natural lighting to capture authentic eye shine and depth. The word \"FLUX\" is painted over it in big, white brush strokes with visible texture.",
>   "image_size": "landscape_4_3",
>   "guidance_scale": 3.5,
>   "sync_mode": false,
>   "num_images": 1,
>   "enable_safety_checker": true,
>   "output_format": "jpeg",
>   "acceleration": "none"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "images": [
>     {
>       "url": "",
>       "content_type": "image/jpeg"
>     }
>   ],
>   "prompt": ""
> }
> ```

> [tab] Redux
>
> **Endpoint:** `POST https://fal.run/fal-ai/flux/schnell/redux`
> **Endpoint ID:** `fal-ai/flux/schnell/redux`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/flux/schnell/redux/playground)
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
>     "fal-ai/flux/schnell/redux",
>     arguments={
>         "image_url": "https://fal.media/files/kangaroo/acQvq-Kmo2lajkgvcEHdv.png"
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
> const result = await fal.subscribe("fal-ai/flux/schnell/redux", {
>   input: {
>       image_url: "https://fal.media/files/kangaroo/acQvq-Kmo2lajkgvcEHdv.png"
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
>   --url https://fal.run/fal-ai/flux/schnell/redux \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://fal.media/files/kangaroo/acQvq-Kmo2lajkgvcEHdv.png"
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> The URL of the image to generate an image from.
>
> **num\_inference\_steps** `integer` (default: 4)
>
> The number of inference steps to perform. Default value: `4`
>
> Range: `1` to `12`
>
> **image\_size** `ImageSize | Enum` (default: landscape\_4\_3)
>
> The size of the generated image. Default value: `landscape_4_3`
>
> Possible values: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`
>
> **seed** `integer`
>
> The same seed and the same prompt given to the same version of the model
> will output the same image every time.
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **num\_images** `integer` (default: 1)
>
> The number of images to generate. Default value: `1`
>
> Range: `1` to `4`
>
> **enable\_safety\_checker** `boolean` (default: true)
>
> If set to true, the safety checker will be enabled. Default value: `true`
>
> **output\_format** `OutputFormatEnum` (default: jpeg)
>
> The format of the generated image. Default value: `"jpeg"`
>
> Possible values: `jpeg`, `png`
>
> **acceleration** `AccelerationEnum` (default: none)
>
> The speed of the generation. The higher the speed, the faster the generation. Default value: `"none"`
>
> Possible values: `none`, `regular`, `high`
>
> ### Output Schema
>
> **images** `list\<Image\>` *required*
>
> The generated image files info.
>
> **timings** `Timings` *required*
>
> **seed** `integer` *required*
>
> Seed of the generated Image. It will be the same value of the one passed in the
> input or the randomly generated that was used in case none was passed.
>
> **has\_nsfw\_concepts** `list\<boolean\>` *required*
>
> Whether the generated images contain NSFW concepts.
>
> **prompt** `string` *required*
>
> The prompt used for generating the image.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://fal.media/files/kangaroo/acQvq-Kmo2lajkgvcEHdv.png",
>   "num_inference_steps": 4,
>   "image_size": "landscape_4_3",
>   "sync_mode": false,
>   "num_images": 1,
>   "enable_safety_checker": true,
>   "output_format": "jpeg",
>   "acceleration": "none"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "images": [
>     {
>       "url": "",
>       "content_type": "image/jpeg"
>     }
>   ],
>   "prompt": ""
> }
> ```

## FLUX.1 \[schnell] - Ultra-Fast Text-to-Image Generation

Transform your ideas into stunning visuals in seconds with FLUX.1 \[schnell], a powerful 12 billion parameter flow transformer designed for production-ready image generation. Whether you're creating content for commercial projects or personal artwork, FLUX.1 \[schnell] delivers exceptional quality in just 1-4 inference steps with sub-second results.

### Key Capabilities

Create high-fidelity images with remarkable speed and precision. FLUX.1 \[schnell] excels at:

* Professional-grade image generation with precise detail control
* Lightning-fast inference (1-4 steps with sub-second results)
* Commercial-ready outputs with full usage rights
* Consistent style maintenance across multiple generations
* Advanced prompt interpretation for accurate results
* Text-to-image and image-to-image generation capabilities

### Popular Use Cases

**Content Creation**
Generate marketing visuals, social media assets, and blog illustrations at scale. Perfect for agencies and content teams who need consistent, high-quality imagery.

**Product Development**\
Create concept art, UI mockups, and design variations for rapid prototyping. Ideal for designers exploring visual directions.

**E-commerce**
Generate product lifestyle images, seasonal campaigns, and A/B testing visuals to boost conversion rates.

**Entertainment**
Produce game assets, storyboard illustrations, and creative concept art for media projects.

### Getting Started

Getting up and running with FLUX.1 \[schnell] is straightforward. Here's how to begin:

1. Install your preferred SDK:

For JavaScript/TypeScript:

```bash theme={null}
npm install --save @fal-ai/client
```

For Python:

```bash theme={null}
pip install fal-client
```

2. Configure your credentials:

```typescript theme={null}
import { fal } from '@fal-ai/client'

fal.config({
  credentials: 'YOUR_FAL_KEY'
})
```

3. Generate your first image:

```typescript theme={null}
const result = await fal.subscribe('fal-ai/flux/schnell', {
  input: {
    prompt: "A serene mountain landscape at sunset, hyperrealistic style",
    num_inference_steps: 4
  }
})
```

### Technical Specifications

FLUX.1 \[schnell] represents the latest in flow-based architecture:

#### Architecture

* **12B parameter flow transformer** optimized for speed
* **Flow-based distillation** for ultra-fast inference
* **1-4 inference steps** (default: 4 steps)
* **Sub-second response times** for fastest generation
* **Commercial usage rights** included

#### API Parameters

* **prompt**: Text description of desired image
* **image\_size**: Various preset sizes or custom dimensions
* **num\_inference\_steps**: Generation steps (default: 4)
* **num\_images**: Number of images to generate (default: 1)
* **seed**: For reproducible results
* **enable\_safety\_checker**: Content safety filtering (default: true)
* **output\_format**: Image format (default: PNG)

#### Performance

* **Sub-second response times** for rapid generation
* **Fastest AI image generation** among FLUX models
* **Concurrent request handling** capability
* **Enterprise-grade reliability** and uptime

### Best Practices

To achieve optimal results with FLUX.1 \[schnell]:

* Write detailed, specific prompts that describe both content and style
* Use 4 steps (default) for balanced speed/quality, adjust as needed
* Leverage the fastest generation for iterative workflows
* Monitor API usage through the dashboard for optimization
* Take advantage of sub-second generation for real-time applications

### Integration Guide

FLUX.1 \[schnell] integrates seamlessly into existing workflows:

1. Set up authentication using environment variables
2. Initialize the client in your application
3. Implement error handling and retries
4. Add response validation
5. Configure webhook endpoints for async processing

### Advanced Features

Take advantage of FLUX.1 \[schnell]'s advanced capabilities:

#### Speed Optimization

FLUX.1 \[schnell] is specifically optimized for ultra-fast generation, making it ideal for applications requiring rapid iteration and real-time image creation.

#### Image Size Control

Generate images at various dimensions with preset sizes or custom width/height configurations to match your specific requirements.

#### Batch Processing

Create multiple variations efficiently with built-in batching support for rapid prototyping and content generation.

#### Turbo Mode

The "schnell" (German for "fast") variant is the turbo mode of the FLUX.1 family, prioritizing speed while maintaining high image quality.

### Pricing and Usage

Your request will cost \$0.003 per megapixel. Images are billed by rounding up to the nearest megapixel. Our transparent, usage-based pricing scales with your needs:

* **Most cost-effective**: \$0.003 per megapixel
* **Commercial usage rights** included
* **No subscription fees** or minimum commitments
* **Fastest generation** with competitive pricing

[View detailed pricing](https://fal.ai/pricing) or [contact sales](mailto:support@fal.ai) for enterprise solutions.

### Support and Resources

We're here to help you succeed with FLUX.1 \[schnell]:

* **Documentation**: Comprehensive API references and guides
* **Community**: Active Discord community for peer support
* **Technical Support**: Direct assistance through support tickets
* **Examples**: Growing library of implementation examples
* **Updates**: Regular model improvements and feature additions

Get started today and experience the fastest AI image generation available. Create your API key now and begin transforming your ideas into stunning visuals with FLUX.1 \[schnell].

[Get Started Now](https://fal.ai/login) | [View Documentation](https://docs.fal.ai/) | [Try FLUX Schnell](https://fal.ai/models/fal-ai/flux/schnell)

## Related

* [FLUX.1 \[dev\]](https://fal.ai/docs/model-api-reference/image-generation-api/flux.1) — Image Generation
* [FLUX.1 \[dev\] Redux](https://fal.ai/docs/model-api-reference/image-generation-api/flux.1-redux) — Image Generation

## Limitations

* `num_inference_steps` range: 1 to 12
* `image_size` restricted to: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`
* `guidance_scale` range: 1 to 20
* `num_images` range: 1 to 4
* `output_format` restricted to: `jpeg`, `png`
* `acceleration` restricted to: `none`, `regular`, `high`
* Content moderation via safety checker
