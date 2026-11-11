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

# Z Image Turbo Inpaint API

> API reference for Z Image Turbo Inpaint. Generate images from text, an image and a mask using Z-Image Turbo, Tongyi-MAI's super-fast 6B model.

> [tab] Inpaint
>
> **Endpoint:** `POST https://fal.run/fal-ai/z-image/turbo/inpaint`
> **Endpoint ID:** `fal-ai/z-image/turbo/inpaint`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/z-image/turbo/inpaint/playground)
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
>     "fal-ai/z-image/turbo/inpaint",
>     arguments={
>         "prompt": "A young Asian woman with long, vibrant purple hair stands on a sunlit sandy beach, posing confidently with her left hand resting on her hip. She gazes directly at the camera with a neutral expression. A sleek black ribbon bow is tied neatly on the right side of her head, just above her ear. She wears a flowing white cotton dress with a fitted bodice and a flared skirt that reaches mid-calf, slightly lifted by a gentle sea breeze. The beach behind her features fine, pale golden sand with subtle footprints, leading to calm turquoise waves under a clear blue sky with soft, wispy clouds. The lighting is natural daylight, casting soft shadows to her left, indicating late afternoon sun. The horizon line is visible in the background, with a faint silhouette of distant dunes. Her skin tone is fair with a natural glow, and her facial features are delicately defined. The composition is centered on her figure, framed from mid-thigh up, with shallow depth of field blurring the distant waves slightly.",
>         "image_url": "https://storage.googleapis.com/falserverless/example_inputs/inpaint-input.jpg",
>         "mask_image_url": "https://storage.googleapis.com/falserverless/whls/z-image-inpaint-mask.jpg"
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
> const result = await fal.subscribe("fal-ai/z-image/turbo/inpaint", {
>   input: {
>       prompt: "A young Asian woman with long, vibrant purple hair stands on a sunlit sandy beach, posing confidently with her left hand resting on her hip. She gazes directly at the camera with a neutral expression. A sleek black ribbon bow is tied neatly on the right side of her head, just above her ear. She wears a flowing white cotton dress with a fitted bodice and a flared skirt that reaches mid-calf, slightly lifted by a gentle sea breeze. The beach behind her features fine, pale golden sand with subtle footprints, leading to calm turquoise waves under a clear blue sky with soft, wispy clouds. The lighting is natural daylight, casting soft shadows to her left, indicating late afternoon sun. The horizon line is visible in the background, with a faint silhouette of distant dunes. Her skin tone is fair with a natural glow, and her facial features are delicately defined. The composition is centered on her figure, framed from mid-thigh up, with shallow depth of field blurring the distant waves slightly.",
>       image_url: "https://storage.googleapis.com/falserverless/example_inputs/inpaint-input.jpg",
>       mask_image_url: "https://storage.googleapis.com/falserverless/whls/z-image-inpaint-mask.jpg"
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
>   --url https://fal.run/fal-ai/z-image/turbo/inpaint \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A young Asian woman with long, vibrant purple hair stands on a sunlit sandy beach, posing confidently with her left hand resting on her hip. She gazes directly at the camera with a neutral expression. A sleek black ribbon bow is tied neatly on the right side of her head, just above her ear. She wears a flowing white cotton dress with a fitted bodice and a flared skirt that reaches mid-calf, slightly lifted by a gentle sea breeze. The beach behind her features fine, pale golden sand with subtle footprints, leading to calm turquoise waves under a clear blue sky with soft, wispy clouds. The lighting is natural daylight, casting soft shadows to her left, indicating late afternoon sun. The horizon line is visible in the background, with a faint silhouette of distant dunes. Her skin tone is fair with a natural glow, and her facial features are delicately defined. The composition is centered on her figure, framed from mid-thigh up, with shallow depth of field blurring the distant waves slightly.",
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/inpaint-input.jpg",
>   "mask_image_url": "https://storage.googleapis.com/falserverless/whls/z-image-inpaint-mask.jpg"
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
> URL of Image for Inpaint generation.
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
> **mask\_image\_url** `string` *required*
>
> URL of Mask for Inpaint generation.
>
> **strength** `float` (default: 1)
>
> The strength of the inpaint conditioning. Default value: `1`
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
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/inpaint-input.jpg",
>   "control_scale": 0.75,
>   "control_start": 0,
>   "control_end": 0.8,
>   "mask_image_url": "https://storage.googleapis.com/falserverless/whls/z-image-inpaint-mask.jpg",
>   "strength": 1
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
>       "height": 888,
>       "url": "https://storage.googleapis.com/falserverless/example_outputs/z-image-inpaint-output.png",
>       "width": 512
>     }
>   ],
>   "prompt": ""
> }
> ```

> [tab] Lora
>
> **Endpoint:** `POST https://fal.run/fal-ai/z-image/turbo/inpaint/lora`
> **Endpoint ID:** `fal-ai/z-image/turbo/inpaint/lora`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/z-image/turbo/inpaint/lora/playground)
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
>     "fal-ai/z-image/turbo/inpaint/lora",
>     arguments={
>         "prompt": "A young Asian woman with long, vibrant purple hair stands on a sunlit sandy beach, posing confidently with her left hand resting on her hip. She gazes directly at the camera with a neutral expression. A sleek black ribbon bow is tied neatly on the right side of her head, just above her ear. She wears a flowing white cotton dress with a fitted bodice and a flared skirt that reaches mid-calf, slightly lifted by a gentle sea breeze. The beach behind her features fine, pale golden sand with subtle footprints, leading to calm turquoise waves under a clear blue sky with soft, wispy clouds. The lighting is natural daylight, casting soft shadows to her left, indicating late afternoon sun. The horizon line is visible in the background, with a faint silhouette of distant dunes. Her skin tone is fair with a natural glow, and her facial features are delicately defined. The composition is centered on her figure, framed from mid-thigh up, with shallow depth of field blurring the distant waves slightly.",
>         "image_url": "https://storage.googleapis.com/falserverless/example_inputs/inpaint-input.jpg",
>         "mask_image_url": "https://storage.googleapis.com/falserverless/whls/z-image-inpaint-mask.jpg"
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
> const result = await fal.subscribe("fal-ai/z-image/turbo/inpaint/lora", {
>   input: {
>       prompt: "A young Asian woman with long, vibrant purple hair stands on a sunlit sandy beach, posing confidently with her left hand resting on her hip. She gazes directly at the camera with a neutral expression. A sleek black ribbon bow is tied neatly on the right side of her head, just above her ear. She wears a flowing white cotton dress with a fitted bodice and a flared skirt that reaches mid-calf, slightly lifted by a gentle sea breeze. The beach behind her features fine, pale golden sand with subtle footprints, leading to calm turquoise waves under a clear blue sky with soft, wispy clouds. The lighting is natural daylight, casting soft shadows to her left, indicating late afternoon sun. The horizon line is visible in the background, with a faint silhouette of distant dunes. Her skin tone is fair with a natural glow, and her facial features are delicately defined. The composition is centered on her figure, framed from mid-thigh up, with shallow depth of field blurring the distant waves slightly.",
>       image_url: "https://storage.googleapis.com/falserverless/example_inputs/inpaint-input.jpg",
>       mask_image_url: "https://storage.googleapis.com/falserverless/whls/z-image-inpaint-mask.jpg"
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
>   --url https://fal.run/fal-ai/z-image/turbo/inpaint/lora \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A young Asian woman with long, vibrant purple hair stands on a sunlit sandy beach, posing confidently with her left hand resting on her hip. She gazes directly at the camera with a neutral expression. A sleek black ribbon bow is tied neatly on the right side of her head, just above her ear. She wears a flowing white cotton dress with a fitted bodice and a flared skirt that reaches mid-calf, slightly lifted by a gentle sea breeze. The beach behind her features fine, pale golden sand with subtle footprints, leading to calm turquoise waves under a clear blue sky with soft, wispy clouds. The lighting is natural daylight, casting soft shadows to her left, indicating late afternoon sun. The horizon line is visible in the background, with a faint silhouette of distant dunes. Her skin tone is fair with a natural glow, and her facial features are delicately defined. The composition is centered on her figure, framed from mid-thigh up, with shallow depth of field blurring the distant waves slightly.",
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/inpaint-input.jpg",
>   "mask_image_url": "https://storage.googleapis.com/falserverless/whls/z-image-inpaint-mask.jpg"
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
> URL of Image for Inpaint generation.
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
> **mask\_image\_url** `string` *required*
>
> URL of Mask for Inpaint generation.
>
> **strength** `float` (default: 1)
>
> The strength of the inpaint conditioning. Default value: `1`
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
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/inpaint-input.jpg",
>   "control_scale": 0.75,
>   "control_start": 0,
>   "control_end": 0.8,
>   "mask_image_url": "https://storage.googleapis.com/falserverless/whls/z-image-inpaint-mask.jpg",
>   "strength": 1,
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
>       "height": 888,
>       "url": "https://storage.googleapis.com/falserverless/example_outputs/z-image-inpaint-output.png",
>       "width": 512
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
* `control_scale` range: 0 to 1
* `control_start` range: 0 to 1
* `control_end` range: 0 to 1
* Content moderation via safety checker
