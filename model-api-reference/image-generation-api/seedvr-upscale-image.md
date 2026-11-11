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

# Seedvr Upscale Image API

> API reference for Seedvr Upscale Image. Use SeedVR2 to upscale your images

> [tab] Image
>
> **Endpoint:** `POST https://fal.run/fal-ai/seedvr/upscale/image`
> **Endpoint ID:** `fal-ai/seedvr/upscale/image`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/seedvr/upscale/image/playground)
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
>     "fal-ai/seedvr/upscale/image",
>     arguments={
>         "image_url": "https://storage.googleapis.com/falserverless/example_inputs/seedvr2/image_in.png"
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
> const result = await fal.subscribe("fal-ai/seedvr/upscale/image", {
>   input: {
>       image_url: "https://storage.googleapis.com/falserverless/example_inputs/seedvr2/image_in.png"
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
>   --url https://fal.run/fal-ai/seedvr/upscale/image \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/seedvr2/image_in.png"
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> The input image to be processed
>
> **upscale\_mode** `UpscaleModeEnum` (default: factor)
>
> The mode to use for the upscale. If 'target', the upscale factor will be calculated based on the target resolution. If 'factor', the upscale factor will be used directly. Default value: `"factor"`
>
> Possible values: `target`, `factor`
>
> **upscale\_factor** `float` (default: 2)
>
> Upscaling factor to be used. Will multiply the dimensions with this factor when `upscale_mode` is `factor`. Default value: `2`
>
> Range: `1` to `10`
>
> **target\_resolution** `TargetResolutionEnum` (default: 1080p)
>
> The target resolution to upscale to when `upscale_mode` is `target`. Default value: `"1080p"`
>
> Possible values: `720p`, `1080p`, `1440p`, `2160p`
>
> **seed** `integer`
>
> The random seed used for the generation process.
>
> **noise\_scale** `float` (default: 0.1)
>
> The noise scale to use for the generation process. Default value: `0.1`
>
> Range: `0` to `1`, step: `0.001`
>
> **output\_format** `OutputFormatEnum` (default: jpg)
>
> The format of the output image. Default value: `"jpg"`
>
> Possible values: `png`, `jpg`, `webp`
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> ### Output Schema
>
> **image** `ImageFile` *required*
>
> Upscaled image file after processing
>
> **seed** `integer` *required*
>
> The random seed used for the generation process.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/seedvr2/image_in.png",
>   "upscale_mode": "factor",
>   "upscale_factor": 2,
>   "target_resolution": "1080p",
>   "noise_scale": 0.1,
>   "output_format": "jpg",
>   "sync_mode": false
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "image": {
>     "content_type": "image/png",
>     "url": "https://storage.googleapis.com/falserverless/example_outputs/seedvr2/image_out.png"
>   }
> }
> ```

> [tab] Seamless
>
> **Endpoint:** `POST https://fal.run/fal-ai/seedvr/upscale/image/seamless`
> **Endpoint ID:** `fal-ai/seedvr/upscale/image/seamless`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/seedvr/upscale/image/seamless/playground)
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
>     "fal-ai/seedvr/upscale/image/seamless",
>     arguments={
>         "image_url": "https://v3b.fal.media/files/b/0a93eb33/dQ_ZwLyeu8P2UJYsa8RDW_seamless_image_in.png"
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
> const result = await fal.subscribe("fal-ai/seedvr/upscale/image/seamless", {
>   input: {
>       image_url: "https://v3b.fal.media/files/b/0a93eb33/dQ_ZwLyeu8P2UJYsa8RDW_seamless_image_in.png"
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
>   --url https://fal.run/fal-ai/seedvr/upscale/image/seamless \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://v3b.fal.media/files/b/0a93eb33/dQ_ZwLyeu8P2UJYsa8RDW_seamless_image_in.png"
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> The input image to be processed
>
> **upscale\_mode** `UpscaleModeEnum` (default: factor)
>
> The mode to use for the upscale. If 'target', the upscale factor will be calculated based on the target resolution. If 'factor', the upscale factor will be used directly. Default value: `"factor"`
>
> Possible values: `target`, `factor`
>
> **upscale\_factor** `float` (default: 2)
>
> Upscaling factor to be used. Will multiply the dimensions with this factor when `upscale_mode` is `factor`. Default value: `2`
>
> Range: `1` to `10`
>
> **target\_resolution** `TargetResolutionEnum` (default: 1080p)
>
> The target resolution to upscale to when `upscale_mode` is `target`. Default value: `"1080p"`
>
> Possible values: `720p`, `1080p`, `1440p`, `2160p`
>
> **seed** `integer`
>
> The random seed used for the generation process.
>
> **noise\_scale** `float` (default: 0.1)
>
> The noise scale to use for the generation process. Default value: `0.1`
>
> Range: `0` to `1`, step: `0.001`
>
> **enable\_safety\_checker** `boolean` (default: true)
>
> If set to true, the safety checker will be enabled. Default value: `true`
>
> **output\_format** `OutputFormatEnum` (default: png)
>
> The format of the output image. Default value: `"png"`
>
> Possible values: `png`, `jpeg`, `webp`
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> ### Output Schema
>
> **image** `ImageFile` *required*
>
> Upscaled image file after processing
>
> **seed** `integer` *required*
>
> The random seed used for the generation process.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://v3b.fal.media/files/b/0a93eb33/dQ_ZwLyeu8P2UJYsa8RDW_seamless_image_in.png",
>   "upscale_mode": "factor",
>   "upscale_factor": 2,
>   "target_resolution": "1080p",
>   "noise_scale": 0.1,
>   "enable_safety_checker": true,
>   "output_format": "png",
>   "sync_mode": false
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "image": {
>     "content_type": "image/png",
>     "url": "https://storage.googleapis.com/falserverless/example_outputs/seedvr2/image_out.png"
>   }
> }
> ```

## Related

* [SeedVR2](https://fal.ai/docs/model-api-reference/video-generation-api/seedvr2) — Video Generation

## Limitations

* `upscale_mode` restricted to: `target`, `factor`
* `upscale_factor` range: 1 to 10
* `target_resolution` restricted to: `720p`, `1080p`, `1440p`, `2160p`
* `noise_scale` range: 0 to 1 (step 0.001)
* `output_format` restricted to: `png`, `jpg`, `webp`
* `output_format` restricted to: `png`, `jpeg`, `webp`
* Content moderation via safety checker
