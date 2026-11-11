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

# Z Image Turbo Tiling API

> API reference for Z Image Turbo Tiling. Generate seamlessly tiling photorealistic images from text using Z-Image Turbo

> [tab] Tiling
>
> **Endpoint:** `POST https://fal.run/fal-ai/z-image/turbo/tiling`
> **Endpoint ID:** `fal-ai/z-image/turbo/tiling`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/z-image/turbo/tiling/playground)
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
>     "fal-ai/z-image/turbo/tiling",
>     arguments={
>         "prompt": "A hyper-realistic, high-resolution 4k texture of an ancient weathered brick wall heavily overgrown with lush green moss and soft lichens. The bricks are aged, featuring deep earthy tones, natural cracks, and gritty textures. Vibrant emerald moss fills the mortar lines and spills over the rough surfaces of the stones. Uniform, flat cinematic lighting ensures no harsh shadows, highlighting the intricate organic details and damp stone surfaces. The composition is a perfectly balanced overhead view, showcasing a rich tapestry of botanical growth and masonry craftsmanship with professional clarity and hyper-detailed grit."
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
> const result = await fal.subscribe("fal-ai/z-image/turbo/tiling", {
>   input: {
>       prompt: "A hyper-realistic, high-resolution 4k texture of an ancient weathered brick wall heavily overgrown with lush green moss and soft lichens. The bricks are aged, featuring deep earthy tones, natural cracks, and gritty textures. Vibrant emerald moss fills the mortar lines and spills over the rough surfaces of the stones. Uniform, flat cinematic lighting ensures no harsh shadows, highlighting the intricate organic details and damp stone surfaces. The composition is a perfectly balanced overhead view, showcasing a rich tapestry of botanical growth and masonry craftsmanship with professional clarity and hyper-detailed grit."
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
>   --url https://fal.run/fal-ai/z-image/turbo/tiling \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A hyper-realistic, high-resolution 4k texture of an ancient weathered brick wall heavily overgrown with lush green moss and soft lichens. The bricks are aged, featuring deep earthy tones, natural cracks, and gritty textures. Vibrant emerald moss fills the mortar lines and spills over the rough surfaces of the stones. Uniform, flat cinematic lighting ensures no harsh shadows, highlighting the intricate organic details and damp stone surfaces. The composition is a perfectly balanced overhead view, showcasing a rich tapestry of botanical growth and masonry craftsmanship with professional clarity and hyper-detailed grit."
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The prompt to generate an image from.
>
> **image\_size** `ImageSize | Enum` (default: square\_hd)
>
> The size of the generated image. Use 'auto' to match the input image size (or 1024x1024 if no image). Default value: `square_hd`
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
> **image\_url** `string`
>
> URL of an image for image-to-image or inpainting. When provided without mask\_image\_url, performs image-to-image; with mask\_image\_url, performs inpainting.
>
> **mask\_image\_url** `string`
>
> URL of a mask image for inpainting. White regions are regenerated, black regions are preserved. Requires image\_url.
>
> **strength** `float` (default: 0.6)
>
> How much to transform the input image. Only used when image\_url is provided. Default value: `0.6`
>
> **tile\_size** `integer` (default: 128)
>
> Tile size in latent space (64 = 512px, 128 = 1024px, 256 = 2048px). Default value: `128`
>
> Range: `32` to `256`
>
> **tile\_stride** `integer` (default: 64)
>
> Tile stride in latent space. (32 = 256px, 64 = 512px, 128 = 1024px). Default value: `64`
>
> Range: `16` to `128`
>
> **tiling\_mode** `TilingModeEnum` (default: both)
>
> Tiling direction: 'both' (omnidirectional), 'horizontal' (left-right only), 'vertical' (top-bottom only). Default value: `"both"`
>
> Possible values: `both`, `horizontal`, `vertical`
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
>   "prompt": "A hyper-realistic, high-resolution 4k texture of an ancient weathered brick wall heavily overgrown with lush green moss and soft lichens. The bricks are aged, featuring deep earthy tones, natural cracks, and gritty textures. Vibrant emerald moss fills the mortar lines and spills over the rough surfaces of the stones. Uniform, flat cinematic lighting ensures no harsh shadows, highlighting the intricate organic details and damp stone surfaces. The composition is a perfectly balanced overhead view, showcasing a rich tapestry of botanical growth and masonry craftsmanship with professional clarity and hyper-detailed grit.",
>   "image_size": "square_hd",
>   "num_inference_steps": 8,
>   "sync_mode": false,
>   "num_images": 1,
>   "enable_safety_checker": true,
>   "output_format": "png",
>   "acceleration": "regular",
>   "enable_prompt_expansion": false,
>   "strength": 0.6,
>   "tile_size": 128,
>   "tile_stride": 64,
>   "tiling_mode": "both"
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
>       "height": 2048,
>       "url": "https://v3b.fal.media/files/b/0a9276c3/6KxxMb0bPmfNLH3DqcuSf_qRUe8cIL.png",
>       "width": 2048
>     }
>   ],
>   "prompt": ""
> }
> ```

> [tab] Lora
>
> **Endpoint:** `POST https://fal.run/fal-ai/z-image/turbo/tiling/lora`
> **Endpoint ID:** `fal-ai/z-image/turbo/tiling/lora`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/z-image/turbo/tiling/lora/playground)
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
>     "fal-ai/z-image/turbo/tiling/lora",
>     arguments={
>         "prompt": "A hyper-realistic, high-resolution 4k texture of an ancient weathered brick wall heavily overgrown with lush green moss and soft lichens. The bricks are aged, featuring deep earthy tones, natural cracks, and gritty textures. Vibrant emerald moss fills the mortar lines and spills over the rough surfaces of the stones. Uniform, flat cinematic lighting ensures no harsh shadows, highlighting the intricate organic details and damp stone surfaces. The composition is a perfectly balanced overhead view, showcasing a rich tapestry of botanical growth and masonry craftsmanship with professional clarity and hyper-detailed grit."
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
> const result = await fal.subscribe("fal-ai/z-image/turbo/tiling/lora", {
>   input: {
>       prompt: "A hyper-realistic, high-resolution 4k texture of an ancient weathered brick wall heavily overgrown with lush green moss and soft lichens. The bricks are aged, featuring deep earthy tones, natural cracks, and gritty textures. Vibrant emerald moss fills the mortar lines and spills over the rough surfaces of the stones. Uniform, flat cinematic lighting ensures no harsh shadows, highlighting the intricate organic details and damp stone surfaces. The composition is a perfectly balanced overhead view, showcasing a rich tapestry of botanical growth and masonry craftsmanship with professional clarity and hyper-detailed grit."
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
>   --url https://fal.run/fal-ai/z-image/turbo/tiling/lora \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A hyper-realistic, high-resolution 4k texture of an ancient weathered brick wall heavily overgrown with lush green moss and soft lichens. The bricks are aged, featuring deep earthy tones, natural cracks, and gritty textures. Vibrant emerald moss fills the mortar lines and spills over the rough surfaces of the stones. Uniform, flat cinematic lighting ensures no harsh shadows, highlighting the intricate organic details and damp stone surfaces. The composition is a perfectly balanced overhead view, showcasing a rich tapestry of botanical growth and masonry craftsmanship with professional clarity and hyper-detailed grit."
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The prompt to generate an image from.
>
> **image\_size** `ImageSize | Enum` (default: square\_hd)
>
> The size of the generated image. Use 'auto' to match the input image size (or 1024x1024 if no image). Default value: `square_hd`
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
> **image\_url** `string`
>
> URL of an image for image-to-image or inpainting. When provided without mask\_image\_url, performs image-to-image; with mask\_image\_url, performs inpainting.
>
> **mask\_image\_url** `string`
>
> URL of a mask image for inpainting. White regions are regenerated, black regions are preserved. Requires image\_url.
>
> **strength** `float` (default: 0.6)
>
> How much to transform the input image. Only used when image\_url is provided. Default value: `0.6`
>
> **tile\_size** `integer` (default: 128)
>
> Tile size in latent space (64 = 512px, 128 = 1024px, 256 = 2048px). Default value: `128`
>
> Range: `32` to `256`
>
> **tile\_stride** `integer` (default: 64)
>
> Tile stride in latent space. (32 = 256px, 64 = 512px, 128 = 1024px). Default value: `64`
>
> Range: `16` to `128`
>
> **tiling\_mode** `TilingModeEnum` (default: both)
>
> Tiling direction: 'both' (omnidirectional), 'horizontal' (left-right only), 'vertical' (top-bottom only). Default value: `"both"`
>
> Possible values: `both`, `horizontal`, `vertical`
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
>   "prompt": "A hyper-realistic, high-resolution 4k texture of an ancient weathered brick wall heavily overgrown with lush green moss and soft lichens. The bricks are aged, featuring deep earthy tones, natural cracks, and gritty textures. Vibrant emerald moss fills the mortar lines and spills over the rough surfaces of the stones. Uniform, flat cinematic lighting ensures no harsh shadows, highlighting the intricate organic details and damp stone surfaces. The composition is a perfectly balanced overhead view, showcasing a rich tapestry of botanical growth and masonry craftsmanship with professional clarity and hyper-detailed grit.",
>   "image_size": "square_hd",
>   "num_inference_steps": 8,
>   "sync_mode": false,
>   "num_images": 1,
>   "enable_safety_checker": true,
>   "output_format": "png",
>   "acceleration": "regular",
>   "enable_prompt_expansion": false,
>   "strength": 0.6,
>   "tile_size": 128,
>   "tile_stride": 64,
>   "tiling_mode": "both",
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
>       "height": 2048,
>       "url": "https://v3b.fal.media/files/b/0a9276c3/6KxxMb0bPmfNLH3DqcuSf_qRUe8cIL.png",
>       "width": 2048
>     }
>   ],
>   "prompt": ""
> }
> ```

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
* `tile_size` range: 32 to 256
* `tile_stride` range: 16 to 128
* `tiling_mode` restricted to: `both`, `horizontal`, `vertical`
* Content moderation via safety checker
