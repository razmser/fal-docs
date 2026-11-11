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

# Z Image Turbo Image To Image API

> API reference for Z Image Turbo Image To Image. Generate images from text and images using Z-Image Turbo, Tongyi-MAI's super-fast 6B model.

> [tab] Image To Image
>
> **Endpoint:** `POST https://fal.run/fal-ai/z-image/turbo/image-to-image`
> **Endpoint ID:** `fal-ai/z-image/turbo/image-to-image`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/z-image/turbo/image-to-image/playground)
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
>     "fal-ai/z-image/turbo/image-to-image",
>     arguments={
>         "prompt": "A young Asian woman with long, vibrant purple hair stands on a sunlit sandy beach, posing confidently with her left hand resting on her hip. She gazes directly at the camera with a neutral expression. A sleek black ribbon bow is tied neatly on the right side of her head, just above her ear. She wears a flowing white cotton dress with a fitted bodice and a flared skirt that reaches mid-calf, slightly lifted by a gentle sea breeze. The beach behind her features fine, pale golden sand with subtle footprints, leading to calm turquoise waves under a clear blue sky with soft, wispy clouds. The lighting is natural daylight, casting soft shadows to her left, indicating late afternoon sun. The horizon line is visible in the background, with a faint silhouette of distant dunes. Her skin tone is fair with a natural glow, and her facial features are delicately defined. The composition is centered on her figure, framed from mid-thigh up, with shallow depth of field blurring the distant waves slightly.",
>         "image_url": "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-i2i-input.png"
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
> const result = await fal.subscribe("fal-ai/z-image/turbo/image-to-image", {
>   input: {
>       prompt: "A young Asian woman with long, vibrant purple hair stands on a sunlit sandy beach, posing confidently with her left hand resting on her hip. She gazes directly at the camera with a neutral expression. A sleek black ribbon bow is tied neatly on the right side of her head, just above her ear. She wears a flowing white cotton dress with a fitted bodice and a flared skirt that reaches mid-calf, slightly lifted by a gentle sea breeze. The beach behind her features fine, pale golden sand with subtle footprints, leading to calm turquoise waves under a clear blue sky with soft, wispy clouds. The lighting is natural daylight, casting soft shadows to her left, indicating late afternoon sun. The horizon line is visible in the background, with a faint silhouette of distant dunes. Her skin tone is fair with a natural glow, and her facial features are delicately defined. The composition is centered on her figure, framed from mid-thigh up, with shallow depth of field blurring the distant waves slightly.",
>       image_url: "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-i2i-input.png"
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
>   --url https://fal.run/fal-ai/z-image/turbo/image-to-image \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A young Asian woman with long, vibrant purple hair stands on a sunlit sandy beach, posing confidently with her left hand resting on her hip. She gazes directly at the camera with a neutral expression. A sleek black ribbon bow is tied neatly on the right side of her head, just above her ear. She wears a flowing white cotton dress with a fitted bodice and a flared skirt that reaches mid-calf, slightly lifted by a gentle sea breeze. The beach behind her features fine, pale golden sand with subtle footprints, leading to calm turquoise waves under a clear blue sky with soft, wispy clouds. The lighting is natural daylight, casting soft shadows to her left, indicating late afternoon sun. The horizon line is visible in the background, with a faint silhouette of distant dunes. Her skin tone is fair with a natural glow, and her facial features are delicately defined. The composition is centered on her figure, framed from mid-thigh up, with shallow depth of field blurring the distant waves slightly.",
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-i2i-input.png"
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
> URL of Image for Image-to-Image generation.
>
> **strength** `float` (default: 0.6)
>
> The strength of the image-to-image conditioning. Default value: `0.6`
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
>   "prompt": "A young Asian woman with long, vibrant purple hair stands on a sunlit sandy beach, posing confidently with her left hand resting on her hip. She gazes directly at the camera with a neutral expression. A sleek black ribbon bow is tied neatly on the right side of her head, just above her ear. She wears a flowing white cotton dress with a fitted bodice and a flared skirt that reaches mid-calf, slightly lifted by a gentle sea breeze. The beach behind her features fine, pale golden sand with subtle footprints, leading to calm turquoise waves under a clear blue sky with soft, wispy clouds. The lighting is natural daylight, casting soft shadows to her left, indicating late afternoon sun. The horizon line is visible in the background, with a faint silhouette of distant dunes. Her skin tone is fair with a natural glow, and her facial features are delicately defined. The composition is centered on her figure, framed from mid-thigh up, with shallow depth of field blurring the distant waves slightly.",
>   "image_size": "auto",
>   "num_inference_steps": 8,
>   "sync_mode": false,
>   "num_images": 1,
>   "enable_safety_checker": true,
>   "output_format": "png",
>   "acceleration": "regular",
>   "enable_prompt_expansion": false,
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-i2i-input.png",
>   "strength": 0.6
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
>       "height": 1728,
>       "url": "https://storage.googleapis.com/falserverless/example_outputs/z-image-turbo-i2i-output.png",
>       "width": 992
>     }
>   ],
>   "prompt": ""
> }
> ```

> [tab] Lora
>
> **Endpoint:** `POST https://fal.run/fal-ai/z-image/turbo/image-to-image/lora`
> **Endpoint ID:** `fal-ai/z-image/turbo/image-to-image/lora`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/z-image/turbo/image-to-image/lora/playground)
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
>     "fal-ai/z-image/turbo/image-to-image/lora",
>     arguments={
>         "prompt": "A young Asian woman with long, vibrant purple hair stands on a sunlit sandy beach, posing confidently with her left hand resting on her hip. She gazes directly at the camera with a neutral expression. A sleek black ribbon bow is tied neatly on the right side of her head, just above her ear. She wears a flowing white cotton dress with a fitted bodice and a flared skirt that reaches mid-calf, slightly lifted by a gentle sea breeze. The beach behind her features fine, pale golden sand with subtle footprints, leading to calm turquoise waves under a clear blue sky with soft, wispy clouds. The lighting is natural daylight, casting soft shadows to her left, indicating late afternoon sun. The horizon line is visible in the background, with a faint silhouette of distant dunes. Her skin tone is fair with a natural glow, and her facial features are delicately defined. The composition is centered on her figure, framed from mid-thigh up, with shallow depth of field blurring the distant waves slightly.",
>         "image_url": "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-i2i-input.png"
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
> const result = await fal.subscribe("fal-ai/z-image/turbo/image-to-image/lora", {
>   input: {
>       prompt: "A young Asian woman with long, vibrant purple hair stands on a sunlit sandy beach, posing confidently with her left hand resting on her hip. She gazes directly at the camera with a neutral expression. A sleek black ribbon bow is tied neatly on the right side of her head, just above her ear. She wears a flowing white cotton dress with a fitted bodice and a flared skirt that reaches mid-calf, slightly lifted by a gentle sea breeze. The beach behind her features fine, pale golden sand with subtle footprints, leading to calm turquoise waves under a clear blue sky with soft, wispy clouds. The lighting is natural daylight, casting soft shadows to her left, indicating late afternoon sun. The horizon line is visible in the background, with a faint silhouette of distant dunes. Her skin tone is fair with a natural glow, and her facial features are delicately defined. The composition is centered on her figure, framed from mid-thigh up, with shallow depth of field blurring the distant waves slightly.",
>       image_url: "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-i2i-input.png"
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
>   --url https://fal.run/fal-ai/z-image/turbo/image-to-image/lora \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A young Asian woman with long, vibrant purple hair stands on a sunlit sandy beach, posing confidently with her left hand resting on her hip. She gazes directly at the camera with a neutral expression. A sleek black ribbon bow is tied neatly on the right side of her head, just above her ear. She wears a flowing white cotton dress with a fitted bodice and a flared skirt that reaches mid-calf, slightly lifted by a gentle sea breeze. The beach behind her features fine, pale golden sand with subtle footprints, leading to calm turquoise waves under a clear blue sky with soft, wispy clouds. The lighting is natural daylight, casting soft shadows to her left, indicating late afternoon sun. The horizon line is visible in the background, with a faint silhouette of distant dunes. Her skin tone is fair with a natural glow, and her facial features are delicately defined. The composition is centered on her figure, framed from mid-thigh up, with shallow depth of field blurring the distant waves slightly.",
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-i2i-input.png"
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
> URL of Image for Image-to-Image generation.
>
> **strength** `float` (default: 0.6)
>
> The strength of the image-to-image conditioning. Default value: `0.6`
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
>   "prompt": "A young Asian woman with long, vibrant purple hair stands on a sunlit sandy beach, posing confidently with her left hand resting on her hip. She gazes directly at the camera with a neutral expression. A sleek black ribbon bow is tied neatly on the right side of her head, just above her ear. She wears a flowing white cotton dress with a fitted bodice and a flared skirt that reaches mid-calf, slightly lifted by a gentle sea breeze. The beach behind her features fine, pale golden sand with subtle footprints, leading to calm turquoise waves under a clear blue sky with soft, wispy clouds. The lighting is natural daylight, casting soft shadows to her left, indicating late afternoon sun. The horizon line is visible in the background, with a faint silhouette of distant dunes. Her skin tone is fair with a natural glow, and her facial features are delicately defined. The composition is centered on her figure, framed from mid-thigh up, with shallow depth of field blurring the distant waves slightly.",
>   "image_size": "auto",
>   "num_inference_steps": 8,
>   "sync_mode": false,
>   "num_images": 1,
>   "enable_safety_checker": true,
>   "output_format": "png",
>   "acceleration": "regular",
>   "enable_prompt_expansion": false,
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/z-image-turbo-i2i-input.png",
>   "strength": 0.6,
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
>       "height": 1728,
>       "url": "https://storage.googleapis.com/falserverless/example_outputs/z-image-turbo-i2i-output.png",
>       "width": 992
>     }
>   ],
>   "prompt": ""
> }
> ```

Tongyi-MAI's Z-Image Turbo delivers image-to-image generation at \$0.005 per megapixel through a 6-billion parameter architecture. Trading raw parameter count for inference optimization, this model processes transformations in 8 steps or fewer while maintaining commercial-grade output quality. Built for developers who need cost-effective image modification at scale without sacrificing control over the transformation process.

**Built for:** Product variant generation | Style transfer workflows | Rapid prototyping iterations

***

## Performance That Scales

At \$0.005 per megapixel, Z-Image Turbo positions 10-15x more cost-effectively than premium image generation alternatives while maintaining the flexibility of adjustable inference steps.

| Metric | Result | Context |
| :- | :- | :- |
| **Inference Steps** | 1-8 steps | Configurable via API, default 8 steps balances quality and speed |
| **Cost per Megapixel** | \$0.005 | 200 megapixels per \$1.00 on fal |
| **Batch Size** | Up to 4 images | Per request via `num_images` parameter |
| **Strength Range** | 0.0-1.0 | Default 0.6, lower values preserve more source structure |
| **Related Endpoints** | [Z-Image Turbo LoRA](https://fal.ai/models/fal-ai/z-image/turbo/image-to-image/lora) | LoRA variant for custom style training |

***

## Image Transformation With Strength Control

Z-Image Turbo uses a diffusion-based architecture optimized for image-to-image conditioning, where you provide both a reference image and a text prompt to guide the transformation. Unlike pure text-to-image models that start from noise, this approach preserves structural elements from your input while applying the changes you specify.

**What this means for you:**

* **Adjustable transformation intensity:** Control how much the output diverges from your source image via the strength parameter (0-1 range), letting you dial in anything from subtle refinements to dramatic reimaginings
* **Multi-image batch processing:** Generate up to 4 variations per request, useful for A/B testing different prompt variations or exploring creative options without separate API calls
* **Flexible resolution handling:** Auto-sizing adapts to your input dimensions, with support for custom image sizes to match your workflow requirements
* **Accelerated inference options:** Three acceleration levels (none, regular, high) let you trade generation time for cost based on your use case, prototype fast, then refine at full quality

***

## Technical Specifications

| Spec | Details |
| :- | :- |
| **Architecture** | Z-Image Turbo |
| **Input Formats** | Image URL (JPEG, PNG, WebP, GIF, AVIF) + text prompt |
| **Output Formats** | JPEG, PNG, WebP (configurable via `output_format`) |
| **Model Size** | 6B parameters |
| **License** | Commercial use enabled |

[API Documentation](https://fal.ai/models/fal-ai/z-image/turbo/image-to-image/api) | [Quickstart Guide](https://docs.fal.ai/model-apis/quickstart) | [Pricing](https://fal.ai/pricing)

***

## How It Stacks Up

**Z-Image Turbo LoRA** – Z-Image Turbo provides the base transformation engine at \$0.005/megapixel, while the [LoRA variant](https://fal.ai/models/fal-ai/z-image/turbo/image-to-image/lora) adds custom style training capabilities for specialized visual treatments. The LoRA endpoint trades base model simplicity for fine-tuned control when you need consistent brand aesthetics or specific artistic styles across multiple generations.

**FASHN Virtual Try-On V1.5** – Z-Image Turbo handles general-purpose image transformation with flexible prompt control for \$0.005/megapixel. [FASHN](https://fal.ai/models/fal-ai/fashn/tryon/v1.5) specializes in garment placement and fit visualization for e-commerce workflows where product accuracy matters more than creative flexibility.

**Image Editing endpoints (Age Progression, Wojak Style)** – Z-Image Turbo offers broad transformation flexibility through natural language prompts, while [specialized editing endpoints](https://fal.ai/models/fal-ai/image-editing/age-progression) provide single-function transformations optimized for specific use cases. Choose Z-Image Turbo when you need multi-purpose image modification without switching between task-specific models.

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
* Content moderation via safety checker
