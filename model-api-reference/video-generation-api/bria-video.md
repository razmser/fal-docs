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

# Bria Video API

> API reference for Bria Video. Automatically remove backgrounds from videos -perfect for creating clean, professional content without a green screen.

> [tab] Background Removal
>
> **Endpoint:** `POST https://fal.run/bria/video/background-removal`
> **Endpoint ID:** `bria/video/background-removal`
>
> > [card] [Try it in the Playground](https://fal.ai/models/bria/video/background-removal/playground)
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
>     "bria/video/background-removal",
>     arguments={
>         "video_url": "https://bria-datasets.s3.us-east-1.amazonaws.com/rmbg_tests/videos/5586521-uhd_3840_2160_25fps_original.mp4"
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
> const result = await fal.subscribe("bria/video/background-removal", {
>   input: {
>       video_url: "https://bria-datasets.s3.us-east-1.amazonaws.com/rmbg_tests/videos/5586521-uhd_3840_2160_25fps_original.mp4"
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
>   --url https://fal.run/bria/video/background-removal \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "video_url": "https://bria-datasets.s3.us-east-1.amazonaws.com/rmbg_tests/videos/5586521-uhd_3840_2160_25fps_original.mp4"
> }'
> ```
>
> ### Input Schema
>
> **video\_url** `string` *required*
>
> Input video to remove background from. Size should be less than 4000x4000 and duration less than 30s.
>
> **background\_color** `BackgroundColorEnum` (default: Black)
>
> Background color. Options: Transparent, Black, White, Gray, Red, Green, Blue, Yellow, Cyan, Magenta, Orange. Default value: `"Black"`
>
> Possible values: `Transparent`, `Black`, `White`, `Gray`, `Red`, `Green`, `Blue`, `Yellow`, `Cyan`, `Magenta`, `Orange`
>
> **output\_container\_and\_codec** `OutputContainerAndCodecEnum` (default: webm\_vp9)
>
> Output container and codec. Options: mp4\_h265, mp4\_h264, webm\_vp9, mov\_h265, mov\_proresks, mkv\_h265, mkv\_h264, mkv\_vp9, gif. Default value: `"webm_vp9"`
>
> Possible values: `mp4_h265`, `mp4_h264`, `webm_vp9`, `mov_h265`, `mov_proresks`, `mkv_h265`, `mkv_h264`, `mkv_vp9`, `gif`
>
> ### Output Schema
>
> **video** `Video | File` *required*
>
> Video with removed background and audio.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "video_url": "https://bria-datasets.s3.us-east-1.amazonaws.com/rmbg_tests/videos/5586521-uhd_3840_2160_25fps_original.mp4",
>   "background_color": "Black",
>   "output_container_and_codec": "webm_vp9"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "url": "",
>     "content_type": "image/png",
>     "file_name": "z9RV14K95DvU.png",
>     "file_size": 4404019
>   }
> }
> ```

> [tab] Increase Resolution
>
> **Endpoint:** `POST https://fal.run/bria/video/increase-resolution`
> **Endpoint ID:** `bria/video/increase-resolution`
>
> > [card] [Try it in the Playground](https://fal.ai/models/bria/video/increase-resolution/playground)
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
>     "bria/video/increase-resolution",
>     arguments={
>         "video_url": "https://bria-datasets.s3.us-east-1.amazonaws.com/video_increase_res/3446608-sd_426_240_25fps.mp4"
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
> const result = await fal.subscribe("bria/video/increase-resolution", {
>   input: {
>       video_url: "https://bria-datasets.s3.us-east-1.amazonaws.com/video_increase_res/3446608-sd_426_240_25fps.mp4"
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
>   --url https://fal.run/bria/video/increase-resolution \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "video_url": "https://bria-datasets.s3.us-east-1.amazonaws.com/video_increase_res/3446608-sd_426_240_25fps.mp4"
> }'
> ```
>
> ### Input Schema
>
> **video\_url** `string` *required*
>
> Input video to increase resolution. Size should be less than 7680,4320 and duration less than 30s.
>
> **desired\_increase** `DesiredIncreaseEnum` (default: 2)
>
> desired\_increase factor. Options: 2x, 4x. Default value: `"2"`
>
> Possible values: `2`, `4`
>
> **output\_container\_and\_codec** `OutputContainerAndCodecEnum` (default: webm\_vp9)
>
> Output container and codec. Options: mp4\_h265, mp4\_h264, webm\_vp9, mov\_h265, mov\_proresks, mkv\_h265, mkv\_h264, mkv\_vp9, gif. Default value: `"webm_vp9"`
>
> Possible values: `mp4_h265`, `mp4_h264`, `webm_vp9`, `mov_h265`, `mov_proresks`, `mkv_h265`, `mkv_h264`, `mkv_vp9`, `gif`
>
> ### Output Schema
>
> **video** `Video | File` *required*
>
> Video with removed background and audio.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "video_url": "https://bria-datasets.s3.us-east-1.amazonaws.com/video_increase_res/3446608-sd_426_240_25fps.mp4",
>   "desired_increase": "2",
>   "output_container_and_codec": "webm_vp9"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "url": "",
>     "content_type": "image/png",
>     "file_name": "z9RV14K95DvU.png",
>     "file_size": 4404019
>   }
> }
> ```

## Related

* [Bria RMBG 2.0](https://fal.ai/docs/model-api-reference/image-generation-api/bria-rmbg-2.0) — Image Generation
* [Bria Expand Image](https://fal.ai/docs/model-api-reference/image-generation-api/bria-expand-image) — Image Generation
* [Bria Eraser](../image-generation-api/bria-eraser.md) — Image Generation
* [Bria Product Shot](../image-generation-api/bria-product-shot.md) — Image Generation
* [Bria Background Replace](https://fal.ai/docs/model-api-reference/image-generation-api/bria-background-replace) — Image Generation
* [Bria GenFill](../image-generation-api/bria-genfill.md) — Image Generation
* [Replace Background](https://fal.ai/docs/model-api-reference/image-generation-api/replace-background) — Image Generation
* [Video](https://fal.ai/docs/model-api-reference/video-generation-api/video) — Video Generation
* [Bria Text-to-Image HD](https://fal.ai/docs/model-api-reference/image-generation-api/bria-text-to-image-hd) — Image Generation
* [Bria Text-to-Image Base](https://fal.ai/docs/model-api-reference/image-generation-api/bria-text-to-image-base) — Image Generation
* [Embed Product](https://fal.ai/docs/model-api-reference/image-generation-api/embed-product) — Image Generation
* [Bria Text-to-Image Fast](https://fal.ai/docs/model-api-reference/image-generation-api/bria-text-to-image-fast) — Image Generation

## Limitations

* `desired_increase` restricted to: `2`, `4`
