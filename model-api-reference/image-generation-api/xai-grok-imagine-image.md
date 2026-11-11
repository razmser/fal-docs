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

# Xai Grok Imagine Image API

> API reference for Xai Grok Imagine Image. Generate highly aesthetic images with xAI's Grok Imagine Image generation model.

> [tab] Grok Imagine Image
>
> **Endpoint:** `POST https://fal.run/xai/grok-imagine-image`
> **Endpoint ID:** `xai/grok-imagine-image`
>
> > [card] [Try it in the Playground](https://fal.ai/models/xai/grok-imagine-image/playground)
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
>     "xai/grok-imagine-image",
>     arguments={
>         "prompt": "Abstract human silhouette, golden particles ready to burst outward representing joy, data visualization style, emotional expression through particles, artistic scientific"
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
> const result = await fal.subscribe("xai/grok-imagine-image", {
>   input: {
>       prompt: "Abstract human silhouette, golden particles ready to burst outward representing joy, data visualization style, emotional expression through particles, artistic scientific"
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
>   --url https://fal.run/xai/grok-imagine-image \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Abstract human silhouette, golden particles ready to burst outward representing joy, data visualization style, emotional expression through particles, artistic scientific"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> Text description of the desired image.
>
> **num\_images** `integer` (default: 1)
>
> Number of images to generate. Default value: `1`
>
> Range: `1` to `4`
>
> **aspect\_ratio** `AspectRatioEnum` (default: 1:1)
>
> Aspect ratio of the generated image. Default value: `"1:1"`
>
> Possible values: `2:1`, `20:9`, `19.5:9`, `16:9`, `4:3`, `3:2`, `1:1`, `2:3`, `3:4`, `9:16`, `9:19.5`, `9:20`, `1:2`
>
> **resolution** `ResolutionEnum` (default: 1k)
>
> Resolution of the generated image. `1k` for standard resolution, `2k` for high resolution. Default value: `"1k"`
>
> Possible values: `1k`, `2k`
>
> **output\_format** `OutputFormatEnum` (default: jpeg)
>
> The format of the generated image. Default value: `"jpeg"`
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
> The URL of the generated image.
>
> **revised\_prompt** `string` *required*
>
> The enhanced prompt that was used to generate the image.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "Abstract human silhouette, golden particles ready to burst outward representing joy, data visualization style, emotional expression through particles, artistic scientific",
>   "num_images": 1,
>   "aspect_ratio": "1:1",
>   "resolution": "1k",
>   "output_format": "jpeg",
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
>       "url": "https://v3b.fal.media/files/b/0a8b90b7/9avg_nKJmcVinjQHJR_Ja.jpg"
>     }
>   ],
>   "revised_prompt": ""
> }
> ```

> [tab] Edit
>
> **Endpoint:** `POST https://fal.run/xai/grok-imagine-image/edit`
> **Endpoint ID:** `xai/grok-imagine-image/edit`
>
> > [card] [Try it in the Playground](https://fal.ai/models/xai/grok-imagine-image/edit/playground)
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
>     "xai/grok-imagine-image/edit",
>     arguments={
>         "prompt": "Make this scene more realistic but still keep the game vibes"
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
> const result = await fal.subscribe("xai/grok-imagine-image/edit", {
>   input: {
>       prompt: "Make this scene more realistic but still keep the game vibes"
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
>   --url https://fal.run/xai/grok-imagine-image/edit \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Make this scene more realistic but still keep the game vibes"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> Text description of the desired image.
>
> **num\_images** `integer` (default: 1)
>
> Number of images to generate. Default value: `1`
>
> Range: `1` to `4`
>
> **resolution** `ResolutionEnum` (default: 1k)
>
> Resolution of the generated image. `1k` for standard resolution, `2k` for high resolution. Default value: `"1k"`
>
> Possible values: `1k`, `2k`
>
> **output\_format** `OutputFormatEnum` (default: jpeg)
>
> The format of the generated image. Default value: `"jpeg"`
>
> Possible values: `jpeg`, `png`, `webp`
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **image\_urls** `list\<string\>`
>
> List of URLs of the images to edit. A maximum of 3 images are supported.
>
> ### Output Schema
>
> **images** `list\<ImageFile\>` *required*
>
> The URL of the edited image.
>
> **revised\_prompt** `string` *required*
>
> The enhanced prompt that was used to generate the image.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "Make this scene more realistic but still keep the game vibes",
>   "num_images": 1,
>   "resolution": "1k",
>   "output_format": "jpeg",
>   "sync_mode": false,
>   "image_urls": [
>     "https://v3b.fal.media/files/b/0a8b911d/Abk8vStrvmSPlzUqI_NN3_image_043.png"
>   ]
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "images": [
>     {
>       "url": "https://v3b.fal.media/files/b/0a8b911d/XMqiVoO2ECXUZEUYmPl2l.jpg"
>     }
>   ],
>   "revised_prompt": ""
> }
> ```

## Related

* [Grok Imagine Image](https://fal.ai/docs/model-api-reference/image-generation-api/grok-imagine-image) — Image Generation

## Limitations

* `num_images` range: 1 to 4
* `resolution` restricted to: `1k`, `2k`
* `output_format` restricted to: `jpeg`, `png`, `webp`
