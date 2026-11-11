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

# Kling Video O3 Standard Video To Video API

> API reference for Kling Video O3 Standard Video To Video. Edit videos using Kling O3 from Kling Team!

> [tab] Edit
>
> **Endpoint:** `POST https://fal.run/fal-ai/kling-video/o3/standard/video-to-video/edit`
> **Endpoint ID:** `fal-ai/kling-video/o3/standard/video-to-video/edit`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/o3/standard/video-to-video/edit/playground)
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
>     "fal-ai/kling-video/o3/standard/video-to-video/edit",
>     arguments={
>         "prompt": "change the main character to be Popeye from @Element1, dark lighting and rain, 3d character. make him sad, with rain dropping on him, dark light on the character.",
>         "video_url": "https://storage.googleapis.com/falserverless/example_inputs/kling-o3/standard-v2v-edit/standard_video_reference.mp4"
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
> const result = await fal.subscribe("fal-ai/kling-video/o3/standard/video-to-video/edit", {
>   input: {
>       prompt: "change the main character to be Popeye from @Element1, dark lighting and rain, 3d character. make him sad, with rain dropping on him, dark light on the character.",
>       video_url: "https://storage.googleapis.com/falserverless/example_inputs/kling-o3/standard-v2v-edit/standard_video_reference.mp4"
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
>   --url https://fal.run/fal-ai/kling-video/o3/standard/video-to-video/edit \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "change the main character to be Popeye from @Element1, dark lighting and rain, 3d character. make him sad, with rain dropping on him, dark light on the character.",
>   "video_url": "https://storage.googleapis.com/falserverless/example_inputs/kling-o3/standard-v2v-edit/standard_video_reference.mp4"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> Text prompt for video generation. Reference video as @Video1.
>
> **video\_url** `string` *required*
>
> Reference video URL. Only .mp4/.mov formats, 3-10s duration, 720-2160px resolution, max 200MB.
>
> **image\_urls** `list\<string\>`
>
> Reference images for style/appearance. Reference in prompt as @Image1, @Image2, etc. Maximum 4 total (elements + reference images) when using video.
>
> **keep\_audio** `boolean` (default: true)
>
> Whether to keep the original audio from the reference video. Default value: `true`
>
> **elements** `list\<KlingV3ImageElementInput\>`
>
> Elements (characters/objects) to include. Reference in prompt as @Element1, @Element2.
>
> **shot\_type** `string` (default: customize)
>
> The type of multi-shot video generation. Default value: `"customize"`
>
> ### Output Schema
>
> **video** `File` *required*
>
> The generated video.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "change the main character to be Popeye from @Element1, dark lighting and rain, 3d character. make him sad, with rain dropping on him, dark light on the character.",
>   "video_url": "https://storage.googleapis.com/falserverless/example_inputs/kling-o3/standard-v2v-edit/standard_video_reference.mp4",
>   "image_urls": null,
>   "keep_audio": true,
>   "elements": [
>     {
>       "frontal_image_url": "https://storage.googleapis.com/falserverless/example_inputs/kling-o3/standard-v2v-edit/element1_front.jpg",
>       "reference_image_urls": [
>         "https://storage.googleapis.com/falserverless/example_inputs/kling-o3/standard-v2v-edit/standard_element1_reference1.jpg"
>       ]
>     }
>   ],
>   "shot_type": "customize"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "content_type": "video/mp4",
>     "file_name": "output.mp4",
>     "file_size": 4322769,
>     "url": "https://storage.googleapis.com/falserverless/example_outputs/kling-o3/standard-v2v-edit/output.mp4"
>   }
> }
> ```

> [tab] Reference
>
> **Endpoint:** `POST https://fal.run/fal-ai/kling-video/o3/standard/video-to-video/reference`
> **Endpoint ID:** `fal-ai/kling-video/o3/standard/video-to-video/reference`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/o3/standard/video-to-video/reference/playground)
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
>     "fal-ai/kling-video/o3/standard/video-to-video/reference",
>     arguments={
>         "prompt": "Replace both character with @Element1 and @Element2",
>         "video_url": "https://storage.googleapis.com/falserverless/example_inputs/kling-o3/standard-v2v-reference/video_reference.mp4"
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
> const result = await fal.subscribe("fal-ai/kling-video/o3/standard/video-to-video/reference", {
>   input: {
>       prompt: "Replace both character with @Element1 and @Element2",
>       video_url: "https://storage.googleapis.com/falserverless/example_inputs/kling-o3/standard-v2v-reference/video_reference.mp4"
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
>   --url https://fal.run/fal-ai/kling-video/o3/standard/video-to-video/reference \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Replace both character with @Element1 and @Element2",
>   "video_url": "https://storage.googleapis.com/falserverless/example_inputs/kling-o3/standard-v2v-reference/video_reference.mp4"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> Text prompt for video generation. Reference video as @Video1.
>
> **video\_url** `string` *required*
>
> Reference video URL. Only .mp4/.mov formats, 3-10s duration, 720-2160px resolution, max 200MB.
>
> **image\_urls** `list\<string\>`
>
> Reference images for style/appearance. Reference in prompt as @Image1, @Image2, etc. Maximum 4 total (elements + reference images) when using video.
>
> **keep\_audio** `boolean` (default: true)
>
> Whether to keep the original audio from the reference video. Default value: `true`
>
> **elements** `list\<KlingV3ImageElementInput\>`
>
> Elements (characters/objects) to include. Reference in prompt as @Element1, @Element2.
>
> **shot\_type** `string` (default: customize)
>
> The type of multi-shot video generation. Default value: `"customize"`
>
> **aspect\_ratio** `AspectRatioEnum` (default: auto)
>
> Aspect ratio. Default value: `"auto"`
>
> Possible values: `auto`, `16:9`, `9:16`, `1:1`
>
> **duration** `Enum`
>
> Video duration in seconds (3-15s for reference video).
>
> Possible values: `3`, `4`, `5`, `6`, `7`, `8`, `9`, `10`, `11`, `12`, `13`, `14`, `15`
>
> ### Output Schema
>
> **video** `File` *required*
>
> The generated video.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "Replace both character with @Element1 and @Element2",
>   "video_url": "https://storage.googleapis.com/falserverless/example_inputs/kling-o3/standard-v2v-reference/video_reference.mp4",
>   "image_urls": null,
>   "keep_audio": false,
>   "elements": [
>     {
>       "frontal_image_url": "https://storage.googleapis.com/falserverless/example_inputs/kling-o3/standard-v2v-reference/element1_front.png",
>       "reference_image_urls": [
>         "https://storage.googleapis.com/falserverless/example_inputs/kling-o3/standard-v2v-reference/element1_reference1.png"
>       ]
>     },
>     {
>       "frontal_image_url": "https://storage.googleapis.com/falserverless/example_inputs/kling-o3/standard-v2v-reference/element2_front.png",
>       "reference_image_urls": [
>         "https://storage.googleapis.com/falserverless/example_inputs/kling-o3/standard-v2v-reference/element2_reference1.png"
>       ]
>     }
>   ],
>   "shot_type": "customize",
>   "aspect_ratio": "auto",
>   "duration": "5"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "content_type": "video/mp4",
>     "file_name": "output.mp4",
>     "file_size": 7992288,
>     "url": "https://storage.googleapis.com/falserverless/example_outputs/kling-o3/standard-v2v-reference/output.mp4"
>   }
> }
> ```

## Related

* [Kling O3 Image to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-image-to-video) — Video Generation
* [Kling O3 Reference to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-reference-to-video) — Video Generation
* [Kling O3 Edit Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-edit-video) — Video Generation
* [Kling O3 Text to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-text-to-video) — Video Generation
* [Kling O3 Reference Video to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-reference-video-to-video) — Video Generation

## Limitations

* `aspect_ratio` restricted to: `auto`, `16:9`, `9:16`, `1:1`
