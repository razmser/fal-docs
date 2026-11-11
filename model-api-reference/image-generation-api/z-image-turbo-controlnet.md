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

# Z Image Turbo Controlnet API

> API reference for Z Image Turbo Controlnet. Generate images from text and edge, depth or pose images using Z-Image Turbo, Tongyi-MAI's super-fast 6B model.

> [tab] Controlnet
>
> **Endpoint:** `POST https://fal.run/fal-ai/z-image/turbo/controlnet`
> **Endpoint ID:** `fal-ai/z-image/turbo/controlnet`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/z-image/turbo/controlnet/playground)
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
>     "fal-ai/z-image/turbo/controlnet",
>     arguments={
>         "prompt": "A single leopard, its spotted golden coat detailed with black rosettes, cautiously peeks its head through dense green foliage. The leopard’s eyes are alert and focused forward, ears perked, whiskers slightly visible. The bushes consist of thick, leafy shrubs with varying shades of green, some leaves partially obscuring the leopard’s muzzle and forehead. Soft natural daylight filters through the canopy above, casting dappled shadows across the animal’s fur and surrounding leaves. The composition is a medium close-up, centered on the leopard’s head emerging from the undergrowth, with shallow depth of field blurring the background vegetation.",
>         "image_url": "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-controlnet-input.jpg"
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
> const result = await fal.subscribe("fal-ai/z-image/turbo/controlnet", {
>   input: {
>       prompt: "A single leopard, its spotted golden coat detailed with black rosettes, cautiously peeks its head through dense green foliage. The leopard’s eyes are alert and focused forward, ears perked, whiskers slightly visible. The bushes consist of thick, leafy shrubs with varying shades of green, some leaves partially obscuring the leopard’s muzzle and forehead. Soft natural daylight filters through the canopy above, casting dappled shadows across the animal’s fur and surrounding leaves. The composition is a medium close-up, centered on the leopard’s head emerging from the undergrowth, with shallow depth of field blurring the background vegetation.",
>       image_url: "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-controlnet-input.jpg"
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
>   --url https://fal.run/fal-ai/z-image/turbo/controlnet \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A single leopard, its spotted golden coat detailed with black rosettes, cautiously peeks its head through dense green foliage. The leopard’s eyes are alert and focused forward, ears perked, whiskers slightly visible. The bushes consist of thick, leafy shrubs with varying shades of green, some leaves partially obscuring the leopard’s muzzle and forehead. Soft natural daylight filters through the canopy above, casting dappled shadows across the animal’s fur and surrounding leaves. The composition is a medium close-up, centered on the leopard’s head emerging from the undergrowth, with shallow depth of field blurring the background vegetation.",
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-controlnet-input.jpg"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The prompt to generate an image from.
>
> **image\_size** `ImageSize | Enum` (default: auto)
>
> The size of the generated image. Default value: `auto`
>
> Possible values: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`, `auto`
>
> **num\_inference\_steps** `integer` (default: 8)
>
> The number of inference steps to perform. Default value: `8`
>
> Range: `1` to `8`
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
> **output\_format** `OutputFormatEnum` (default: png)
>
> The format of the generated image. Default value: `"png"`
>
> Possible values: `jpeg`, `png`, `webp`
>
> **acceleration** `AccelerationEnum` (default: regular)
>
> The acceleration level to use. Default value: `"regular"`
>
> Possible values: `none`, `regular`, `high`
>
> **enable\_prompt\_expansion** `boolean` (default: false)
>
> Whether to enable prompt expansion. Note: this will increase the price by 0.0025 credits per request.
>
> **image\_url** `string` *required*
>
> URL of Image for ControlNet generation.
>
> **control\_scale** `float` (default: 0.75)
>
> The scale of the controlnet conditioning. Default value: `0.75`
>
> Range: `0` to `1`
>
> **control\_start** `float` (default: 0)
>
> The start of the controlnet conditioning.
>
> Range: `0` to `1`
>
> **control\_end** `float` (default: 0.8)
>
> The end of the controlnet conditioning. Default value: `0.8`
>
> Range: `0` to `1`
>
> **preprocess** `Enum` (default: none)
>
> What kind of preprocessing to apply to the image, if any. Default value: `none`
>
> Possible values: `none`, `canny`, `depth`, `pose`
>
> ### Output Schema
>
> **images** `list\<ImageFile\>` *required*
>
> The generated image files info.
>
> **timings** `Timings` *required*
>
> The timings of the generation process.
>
> **seed** `integer` *required*
>
> Seed of the generated Image. It will be the same value of the one passed in the input or the randomly generated that was used in case none was passed.
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
>   "prompt": "A single leopard, its spotted golden coat detailed with black rosettes, cautiously peeks its head through dense green foliage. The leopard’s eyes are alert and focused forward, ears perked, whiskers slightly visible. The bushes consist of thick, leafy shrubs with varying shades of green, some leaves partially obscuring the leopard’s muzzle and forehead. Soft natural daylight filters through the canopy above, casting dappled shadows across the animal’s fur and surrounding leaves. The composition is a medium close-up, centered on the leopard’s head emerging from the undergrowth, with shallow depth of field blurring the background vegetation.",
>   "image_size": "auto",
>   "num_inference_steps": 8,
>   "sync_mode": false,
>   "num_images": 1,
>   "enable_safety_checker": true,
>   "output_format": "png",
>   "acceleration": "regular",
>   "enable_prompt_expansion": false,
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-controlnet-input.jpg",
>   "control_scale": 0.75,
>   "control_start": 0,
>   "control_end": 0.8,
>   "preprocess": "none"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "images": [
>     {
>       "content_type": "image/png",
>       "height": 1024,
>       "url": "https://storage.googleapis.com/falserverless/example_outputs/z-image-turbo-controlnet-output.jpg",
>       "width": 1536
>     }
>   ],
>   "prompt": ""
> }
> ```

> [tab] Lora
>
> **Endpoint:** `POST https://fal.run/fal-ai/z-image/turbo/controlnet/lora`
> **Endpoint ID:** `fal-ai/z-image/turbo/controlnet/lora`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/z-image/turbo/controlnet/lora/playground)
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
>     "fal-ai/z-image/turbo/controlnet/lora",
>     arguments={
>         "prompt": "A single leopard, its spotted golden coat detailed with black rosettes, cautiously peeks its head through dense green foliage. The leopard’s eyes are alert and focused forward, ears perked, whiskers slightly visible. The bushes consist of thick, leafy shrubs with varying shades of green, some leaves partially obscuring the leopard’s muzzle and forehead. Soft natural daylight filters through the canopy above, casting dappled shadows across the animal’s fur and surrounding leaves. The composition is a medium close-up, centered on the leopard’s head emerging from the undergrowth, with shallow depth of field blurring the background vegetation.",
>         "image_url": "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-controlnet-input.jpg"
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
> const result = await fal.subscribe("fal-ai/z-image/turbo/controlnet/lora", {
>   input: {
>       prompt: "A single leopard, its spotted golden coat detailed with black rosettes, cautiously peeks its head through dense green foliage. The leopard’s eyes are alert and focused forward, ears perked, whiskers slightly visible. The bushes consist of thick, leafy shrubs with varying shades of green, some leaves partially obscuring the leopard’s muzzle and forehead. Soft natural daylight filters through the canopy above, casting dappled shadows across the animal’s fur and surrounding leaves. The composition is a medium close-up, centered on the leopard’s head emerging from the undergrowth, with shallow depth of field blurring the background vegetation.",
>       image_url: "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-controlnet-input.jpg"
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
>   --url https://fal.run/fal-ai/z-image/turbo/controlnet/lora \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A single leopard, its spotted golden coat detailed with black rosettes, cautiously peeks its head through dense green foliage. The leopard’s eyes are alert and focused forward, ears perked, whiskers slightly visible. The bushes consist of thick, leafy shrubs with varying shades of green, some leaves partially obscuring the leopard’s muzzle and forehead. Soft natural daylight filters through the canopy above, casting dappled shadows across the animal’s fur and surrounding leaves. The composition is a medium close-up, centered on the leopard’s head emerging from the undergrowth, with shallow depth of field blurring the background vegetation.",
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-controlnet-input.jpg"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The prompt to generate an image from.
>
> **image\_size** `ImageSize | Enum` (default: auto)
>
> The size of the generated image. Default value: `auto`
>
> Possible values: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`, `auto`
>
> **num\_inference\_steps** `integer` (default: 8)
>
> The number of inference steps to perform. Default value: `8`
>
> Range: `1` to `8`
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
> **output\_format** `OutputFormatEnum` (default: png)
>
> The format of the generated image. Default value: `"png"`
>
> Possible values: `jpeg`, `png`, `webp`
>
> **acceleration** `AccelerationEnum` (default: regular)
>
> The acceleration level to use. Default value: `"regular"`
>
> Possible values: `none`, `regular`, `high`
>
> **enable\_prompt\_expansion** `boolean` (default: false)
>
> Whether to enable prompt expansion. Note: this will increase the price by 0.0025 credits per request.
>
> **image\_url** `string` *required*
>
> URL of Image for ControlNet generation.
>
> **control\_scale** `float` (default: 0.75)
>
> The scale of the controlnet conditioning. Default value: `0.75`
>
> Range: `0` to `1`
>
> **control\_start** `float` (default: 0)
>
> The start of the controlnet conditioning.
>
> Range: `0` to `1`
>
> **control\_end** `float` (default: 0.8)
>
> The end of the controlnet conditioning. Default value: `0.8`
>
> Range: `0` to `1`
>
> **preprocess** `Enum` (default: none)
>
> What kind of preprocessing to apply to the image, if any. Default value: `none`
>
> Possible values: `none`, `canny`, `depth`, `pose`
>
> **loras** `list\<LoRAInput\>`
>
> List of LoRA weights to apply (maximum 3).
>
> ### Output Schema
>
> **images** `list\<ImageFile\>` *required*
>
> The generated image files info.
>
> **timings** `Timings` *required*
>
> The timings of the generation process.
>
> **seed** `integer` *required*
>
> Seed of the generated Image. It will be the same value of the one passed in the input or the randomly generated that was used in case none was passed.
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
>   "prompt": "A single leopard, its spotted golden coat detailed with black rosettes, cautiously peeks its head through dense green foliage. The leopard’s eyes are alert and focused forward, ears perked, whiskers slightly visible. The bushes consist of thick, leafy shrubs with varying shades of green, some leaves partially obscuring the leopard’s muzzle and forehead. Soft natural daylight filters through the canopy above, casting dappled shadows across the animal’s fur and surrounding leaves. The composition is a medium close-up, centered on the leopard’s head emerging from the undergrowth, with shallow depth of field blurring the background vegetation.",
>   "image_size": "auto",
>   "num_inference_steps": 8,
>   "sync_mode": false,
>   "num_images": 1,
>   "enable_safety_checker": true,
>   "output_format": "png",
>   "acceleration": "regular",
>   "enable_prompt_expansion": false,
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-controlnet-input.jpg",
>   "control_scale": 0.75,
>   "control_start": 0,
>   "control_end": 0.8,
>   "preprocess": "none",
>   "loras": []
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "images": [
>     {
>       "content_type": "image/png",
>       "height": 1024,
>       "url": "https://storage.googleapis.com/falserverless/example_outputs/z-image-turbo-controlnet-output.jpg",
>       "width": 1536
>     }
>   ],
>   "prompt": ""
> }
> ```

Tongyi-MAI's Z-Image Turbo delivers ControlNet-guided image generation at \$0.0065 per megapixel through a 6-billion parameter architecture. This model trades raw parameter count for specialized control mechanisms, canny edge detection, depth mapping, and pose guidance, that preserve structural fidelity during image-to-image transformations. Built for designers and developers who need precise spatial control without the inference overhead of larger diffusion models.

**Use Cases:** Product visualization with reference geometry | Character pose transfer workflows | Architectural rendering from depth maps

***

## Performance

Z-Image Turbo operates at roughly 3-5x more cost-effective rates than traditional ControlNet implementations by optimizing the 6B parameter base for rapid inference. At \$0.0065 per megapixel, you're running 153 megapixels per dollar, ideal for batch processing workflows where structural guidance matters more than photorealistic perfection.

| Metric | Result | Context |
| :- | :- | :- |
| **Model Size** | 6 billion parameters | Optimized for inference speed vs 70B+ alternatives |
| **Inference Steps** | 1-8 configurable | Default 8 steps balances quality and latency |
| **Cost per Megapixel** | \$0.0065 | 153 megapixels per \$1.00 on fal |
| **Control Methods** | 4 preprocessing modes | None, canny edge, depth map, pose detection |
| **Batch Generation** | Up to 4 images per request | Parallel generation with shared control input |
| **Related Endpoints** | [Standard image-to-image](https://fal.ai/models/fal-ai/z-image/turbo/image-to-image), [LoRA variants](https://fal.ai/models/fal-ai/z-image/turbo/controlnet/lora) | ControlNet vs direct transformation vs custom training |

***

## Structural Control Without Compromise

Z-Image Turbo routes your prompt through three parallel conditioning pathways: text embedding, reference image structure, and optional preprocessing filters. Unlike pure text-to-image models that hallucinate spatial relationships, this architecture extracts edge maps, depth channels, or skeletal poses from your input, then enforces those constraints during diffusion.

**What this means for you:**

* **Configurable control strength (0-1 scale):** Dial conditioning intensity from 0.9 for strict adherence to 0.3 for loose interpretation, critical when your reference image has good composition but needs significant style deviation
* **Temporal control windowing:** Apply ControlNet guidance only during steps 0-40% of generation (configurable start/end), letting early diffusion lock structure while late steps refine aesthetics
* **Four preprocessing modes:** Feed raw images directly or auto-extract canny edges (sharp boundaries), depth maps (spatial layering), or pose skeletons (human/character positioning) without external tools
* **Multi-format output with safety:** Generate 1-4 variants simultaneously in JPEG, PNG, or WebP with optional built-in safety filtering, batch testing style variations while maintaining structural consistency

***

## Technical Specifications

| Spec | Details |
| :- | :- |
| **Architecture** | Z-Image Turbo 6B |
| **Input Formats** | Text prompt + reference image URL (JPEG, PNG, WebP, GIF, AVIF) |
| **Output Formats** | JPEG, PNG, WebP with configurable dimensions |
| **Preprocessing Options** | None, Canny edge detection, Depth estimation, Pose detection |
| **Control Parameters** | Scale (0-1), temporal start/end windowing, inference steps (1-8) |
| **License** | Commercial use permitted |

[API Documentation](https://fal.ai/models/fal-ai/z-image/turbo/controlnet/api) | [Quickstart Guide](https://docs.fal.ai/model-apis/quickstart) | [Enterprise Pricing](https://fal.ai/pricing)

***

## How It Stacks Up

\*\*Z-Image Turbo Standard ($0.0065/MP)** – The [ControlNet variant](https://fal.ai/models/fal-ai/z-image/turbo/controlnet) adds structural guidance preprocessing for $0.0065 per megapixel, same base cost. [Standard image-to-image](https://fal.ai/models/fal-ai/z-image/turbo/image-to-image) prioritizes direct style transfer without intermediate edge/depth extraction, ideal for texture swaps and color grading where spatial relationships already match your target. ControlNet trades processing simplicity for precise geometric control when your reference structure needs enforcement.

**FASHN Virtual Try-On V1.5** – Z-Image Turbo ControlNet offers general-purpose structural conditioning across edge, depth, and pose modalities for diverse creative workflows. [FASHN](https://fal.ai/models/fal-ai/fashn/tryon/v1.5) specializes in garment-to-body fitting with proprietary try-on algorithms optimized for fashion e-commerce, trading generality for domain-specific accuracy in clothing visualization.

## Related

* [Z-Image Turbo](z-image-turbo.md) — Image Generation
* [Z Image Base](z-image-base.md) — Image Generation
* [Z Image Base (LoRA)](https://fal.ai/docs/model-api-reference/image-generation-api/z-image-base-lora) — Image Generation
* [Z-Image Turbo Seamless Tiling](https://fal.ai/docs/model-api-reference/image-generation-api/z-image-turbo-seamless-tiling) — Image Generation

## Limitations

* `image_size` restricted to: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`, `auto`
* `num_inference_steps` range: 1 to 8
* `num_images` range: 1 to 4
* `output_format` restricted to: `jpeg`, `png`, `webp`
* `acceleration` restricted to: `none`, `regular`, `high`
* `control_scale` range: 0 to 1
* `control_start` range: 0 to 1
* `control_end` range: 0 to 1
* `preprocess` restricted to: `none`, `canny`, `depth`, `pose`
* Content moderation via safety checker
