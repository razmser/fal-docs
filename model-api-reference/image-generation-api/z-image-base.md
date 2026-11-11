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

# Z Image Base API

> API reference for Z Image Base. Z-Image is the foundation model of the Z- Image family, engineered for good quality, robust generative diversity, broad stylistic coverage, and precise prompt adherence

> [tab] Base
>
> **Endpoint:** `POST https://fal.run/fal-ai/z-image/base`
> **Endpoint ID:** `fal-ai/z-image/base`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/z-image/base/playground)
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
>     "fal-ai/z-image/base",
>     arguments={
>         "prompt": "Grandmother knitting by a window, an empty chair by her"
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
> const result = await fal.subscribe("fal-ai/z-image/base", {
>   input: {
>       prompt: "Grandmother knitting by a window, an empty chair by her"
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
>   --url https://fal.run/fal-ai/z-image/base \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Grandmother knitting by a window, an empty chair by her"
> }'
> ```
>
> ### Input Schema
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
> **num\_inference\_steps** `integer` (default: 28)
>
> The number of inference steps to perform. Default value: `28`
>
> Range: `1` to `50`
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
> **guidance\_scale** `float` (default: 4)
>
> The guidance scale to use for the image generation. Default value: `4`
>
> Range: `1` to `20`
>
> **negative\_prompt** `string`
>
> The negative prompt to use for the image generation. Default value: `""`
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
>   "prompt": "Grandmother knitting by a window, an empty chair by her",
>   "image_size": "landscape_4_3",
>   "num_inference_steps": 28,
>   "sync_mode": false,
>   "num_images": 1,
>   "enable_safety_checker": true,
>   "output_format": "png",
>   "acceleration": "regular",
>   "guidance_scale": 4,
>   "negative_prompt": ""
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
>       "height": 768,
>       "url": "https://v3b.fal.media/files/b/0a8c18a5/1z0k9F1YLgz4qCr64jCBa_r2uqRyDg.png",
>       "width": 1024
>     }
>   ],
>   "prompt": ""
> }
> ```

> [tab] Lora
>
> **Endpoint:** `POST https://fal.run/fal-ai/z-image/base/lora`
> **Endpoint ID:** `fal-ai/z-image/base/lora`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/z-image/base/lora/playground)
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
>     "fal-ai/z-image/base/lora",
>     arguments={
>         "prompt": "Grandmother knitting by a window, an empty chair by her"
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
> const result = await fal.subscribe("fal-ai/z-image/base/lora", {
>   input: {
>       prompt: "Grandmother knitting by a window, an empty chair by her"
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
>   --url https://fal.run/fal-ai/z-image/base/lora \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Grandmother knitting by a window, an empty chair by her"
> }'
> ```
>
> ### Input Schema
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
> **num\_inference\_steps** `integer` (default: 28)
>
> The number of inference steps to perform. Default value: `28`
>
> Range: `1` to `50`
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
> **guidance\_scale** `float` (default: 4)
>
> The guidance scale to use for the image generation. Default value: `4`
>
> Range: `1` to `20`
>
> **negative\_prompt** `string`
>
> The negative prompt to use for the image generation. Default value: `""`
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
>   "prompt": "Grandmother knitting by a window, an empty chair by her",
>   "image_size": "landscape_4_3",
>   "num_inference_steps": 28,
>   "sync_mode": false,
>   "num_images": 1,
>   "enable_safety_checker": true,
>   "output_format": "png",
>   "acceleration": "regular",
>   "guidance_scale": 4,
>   "negative_prompt": "",
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
>       "height": 768,
>       "url": "https://v3b.fal.media/files/b/0a8c18a5/1z0k9F1YLgz4qCr64jCBa_r2uqRyDg.png",
>       "width": 1024
>     }
>   ],
>   "prompt": ""
> }
> ```

## Related

* [Z-Image Turbo](z-image-turbo.md) — Image Generation
* [Z Image Base (LoRA)](https://fal.ai/docs/model-api-reference/image-generation-api/z-image-base-lora) — Image Generation
* [Z-Image Turbo Seamless Tiling](https://fal.ai/docs/model-api-reference/image-generation-api/z-image-turbo-seamless-tiling) — Image Generation
* [Z Image Base](z-image-base.md) — Image Generation

## Limitations

* `image_size` restricted to: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`
* `num_inference_steps` range: 1 to 50
* `num_images` range: 1 to 4
* `output_format` restricted to: `jpeg`, `png`, `webp`
* `acceleration` restricted to: `none`, `regular`, `high`
* `guidance_scale` range: 1 to 20
* Content moderation via safety checker
