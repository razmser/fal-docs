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

# Gpt Image 1.5 API

> API reference for Gpt Image 1.5. GPT Image 1.5 generates high-fidelity images with strong prompt adherence, preserving composition, lighting, and fine-grained detail.

> [tab] Gpt Image 1.5
>
> **Endpoint:** `POST https://fal.run/fal-ai/gpt-image-1.5`
> **Endpoint ID:** `fal-ai/gpt-image-1.5`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/gpt-image-1.5/playground)
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
>     "fal-ai/gpt-image-1.5",
>     arguments={
>         "prompt": "create a realistic image taken with iphone at these coordinates 41°43′32″N 49°56′49″W 15 April 1912"
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
> const result = await fal.subscribe("fal-ai/gpt-image-1.5", {
>   input: {
>       prompt: "create a realistic image taken with iphone at these coordinates 41°43′32″N 49°56′49″W 15 April 1912"
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
>   --url https://fal.run/fal-ai/gpt-image-1.5 \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "create a realistic image taken with iphone at these coordinates 41°43′32″N 49°56′49″W 15 April 1912"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The prompt for image generation
>
> **image\_size** `ImageSizeEnum` (default: 1024x1024)
>
> Aspect ratio for the generated image Default value: `"1024x1024"`
>
> Possible values: `1024x1024`, `1536x1024`, `1024x1536`
>
> **background** `BackgroundEnum` (default: auto)
>
> Background for the generated image Default value: `"auto"`
>
> Possible values: `auto`, `transparent`, `opaque`
>
> **quality** `QualityEnum` (default: high)
>
> Quality for the generated image Default value: `"high"`
>
> Possible values: `low`, `medium`, `high`
>
> **num\_images** `integer` (default: 1)
>
> Number of images to generate Default value: `1`
>
> Range: `1` to `4`
>
> **output\_format** `OutputFormatEnum` (default: png)
>
> Output format for the images Default value: `"png"`
>
> Possible values: `jpeg`, `png`, `webp`
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> ### Output Schema
>
> **images** `list\<ImageFile\>` *required*
>
> The generated images.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "create a realistic image taken with iphone at these coordinates 41°43′32″N 49°56′49″W 15 April 1912",
>   "image_size": "1024x1024",
>   "background": "auto",
>   "quality": "high",
>   "num_images": 1,
>   "output_format": "png",
>   "sync_mode": false
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
>       "file_name": "EnWrO3XWjPE0nxBDpaQrj.png",
>       "height": 1024,
>       "url": "https://v3b.fal.media/files/b/0a869129/EnWrO3XWjPE0nxBDpaQrj.png",
>       "width": 1024
>     }
>   ]
> }
> ```

> [tab] Edit
>
> **Endpoint:** `POST https://fal.run/fal-ai/gpt-image-1.5/edit`
> **Endpoint ID:** `fal-ai/gpt-image-1.5/edit`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/gpt-image-1.5/edit/playground)
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
>     "fal-ai/gpt-image-1.5/edit",
>     arguments={
>         "prompt": "Same workers, same beam, same lunch boxes - but they're all on their phones now. One is taking a selfie. One is on a call looking annoyed. Same danger, new priorities. A hard hat has AirPods.",
>         "image_urls": [
>             "https://v3b.fal.media/files/b/0a8691af/9Se_1_VX1wzTjjTOpWbs9_bb39c2eb-1a41-4749-b1d0-cf134abc8bbf.png"
>         ]
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
> const result = await fal.subscribe("fal-ai/gpt-image-1.5/edit", {
>   input: {
>       prompt: "Same workers, same beam, same lunch boxes - but they're all on their phones now. One is taking a selfie. One is on a call looking annoyed. Same danger, new priorities. A hard hat has AirPods.",
>       image_urls: [
>         "https://v3b.fal.media/files/b/0a8691af/9Se_1_VX1wzTjjTOpWbs9_bb39c2eb-1a41-4749-b1d0-cf134abc8bbf.png"
>       ]
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
>   --url https://fal.run/fal-ai/gpt-image-1.5/edit \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Same workers, same beam, same lunch boxes - but they'\''re all on their phones now. One is taking a selfie. One is on a call looking annoyed. Same danger, new priorities. A hard hat has AirPods.",
>   "image_urls": [
>     "https://v3b.fal.media/files/b/0a8691af/9Se_1_VX1wzTjjTOpWbs9_bb39c2eb-1a41-4749-b1d0-cf134abc8bbf.png"
>   ]
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The prompt for image generation
>
> **image\_urls** `list\<string\>` *required*
>
> The URLs of the images to use as a reference for the generation.
>
> **image\_size** `ImageSizeEnum` (default: auto)
>
> Aspect ratio for the generated image Default value: `"auto"`
>
> Possible values: `auto`, `1024x1024`, `1536x1024`, `1024x1536`
>
> **background** `BackgroundEnum` (default: auto)
>
> Background for the generated image Default value: `"auto"`
>
> Possible values: `auto`, `transparent`, `opaque`
>
> **quality** `QualityEnum` (default: high)
>
> Quality for the generated image Default value: `"high"`
>
> Possible values: `low`, `medium`, `high`
>
> **input\_fidelity** `InputFidelityEnum` (default: high)
>
> Input fidelity for the generated image Default value: `"high"`
>
> Possible values: `low`, `high`
>
> **num\_images** `integer` (default: 1)
>
> Number of images to generate Default value: `1`
>
> Range: `1` to `4`
>
> **output\_format** `OutputFormatEnum` (default: png)
>
> Output format for the images Default value: `"png"`
>
> Possible values: `jpeg`, `png`, `webp`
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **mask\_image\_url** `string`
>
> The URL of the mask image to use for the generation. This indicates what part of the image to edit.
>
> ### Output Schema
>
> **images** `list\<ImageFile\>` *required*
>
> The generated images.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "Same workers, same beam, same lunch boxes - but they're all on their phones now. One is taking a selfie. One is on a call looking annoyed. Same danger, new priorities. A hard hat has AirPods.",
>   "image_urls": [
>     "https://v3b.fal.media/files/b/0a8691af/9Se_1_VX1wzTjjTOpWbs9_bb39c2eb-1a41-4749-b1d0-cf134abc8bbf.png"
>   ],
>   "image_size": "auto",
>   "background": "auto",
>   "quality": "high",
>   "input_fidelity": "high",
>   "num_images": 1,
>   "output_format": "png",
>   "sync_mode": false
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
>       "file_name": "yUt7tifLSbg1WzWWgfj2o.png",
>       "height": 1024,
>       "url": "https://v3b.fal.media/files/b/0a8691b0/yUt7tifLSbg1WzWWgfj2o.png",
>       "width": 1024
>     }
>   ]
> }
> ```

## Related

* [GPT-Image 1.5](gpt-image-1.5.md) — Image Generation

## Limitations

* `image_size` restricted to: `1024x1024`, `1536x1024`, `1024x1536`
* `background` restricted to: `auto`, `transparent`, `opaque`
* `quality` restricted to: `low`, `medium`, `high`
* `num_images` range: 1 to 4
* `output_format` restricted to: `jpeg`, `png`, `webp`
* `image_size` restricted to: `auto`, `1024x1024`, `1536x1024`, `1024x1536`
* `input_fidelity` restricted to: `low`, `high`
