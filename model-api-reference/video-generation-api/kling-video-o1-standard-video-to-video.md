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

# Kling Video O1 Standard Video To Video API

> API reference for Kling Video O1 Standard Video To Video. Edit an existing video using natural-language instructions, transforming subjects, settings, and style while retaining the original motion str

> [tab] Edit
>
> **Endpoint:** `POST https://fal.run/fal-ai/kling-video/o1/standard/video-to-video/edit`
> **Endpoint ID:** `fal-ai/kling-video/o1/standard/video-to-video/edit`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/o1/standard/video-to-video/edit/playground)
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
>     "fal-ai/kling-video/o1/standard/video-to-video/edit",
>     arguments={
>         "prompt": "Replace the character in the video with @Element1, maintaining the same movements and camera angles. Transform the landscape into @Image1",
>         "video_url": "https://v3b.fal.media/files/b/rabbit/ku8_Wdpf-oTbGRq4lB5DU_output.mp4"
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
> const result = await fal.subscribe("fal-ai/kling-video/o1/standard/video-to-video/edit", {
>   input: {
>       prompt: "Replace the character in the video with @Element1, maintaining the same movements and camera angles. Transform the landscape into @Image1",
>       video_url: "https://v3b.fal.media/files/b/rabbit/ku8_Wdpf-oTbGRq4lB5DU_output.mp4"
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
>   --url https://fal.run/fal-ai/kling-video/o1/standard/video-to-video/edit \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Replace the character in the video with @Element1, maintaining the same movements and camera angles. Transform the landscape into @Image1",
>   "video_url": "https://v3b.fal.media/files/b/rabbit/ku8_Wdpf-oTbGRq4lB5DU_output.mp4"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> Use @Element1, @Element2 to reference elements and @Image1, @Image2 to reference images in order.
>
> **video\_url** `string` *required*
>
> Reference video URL. Only .mp4/.mov formats supported, 3-10 seconds duration, 720-2160px resolution, max 200MB.
>
> Max file size: 200.0MB, Min width: 720px, Min height: 720px, Max width: 2160px, Max height: 2160px, Min duration: 3.0s, Max duration: 10.05s, Min FPS: 24.0, Max FPS: 60.0, Timeout: 30.0s
>
> **keep\_audio** `boolean` (default: false)
>
> Whether to keep the original audio from the video.
>
> **image\_urls** `list\<string\>`
>
> Reference images for style/appearance. Reference in prompt as @Image1, @Image2, etc. Maximum 4 total (elements + reference images) when using video.
>
> **elements** `list\<OmniVideoElementInput\>`
>
> Elements (characters/objects) to include. Reference in prompt as @Element1, @Element2, etc. Maximum 4 total (elements + reference images) when using video.
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
>   "prompt": "Replace the character in the video with @Element1, maintaining the same movements and camera angles. Transform the landscape into @Image1",
>   "video_url": "https://v3b.fal.media/files/b/rabbit/ku8_Wdpf-oTbGRq4lB5DU_output.mp4",
>   "keep_audio": false,
>   "image_urls": [
>     "https://v3b.fal.media/files/b/lion/MKvhFko5_wYnfORYacNII_AgPt8v25Wt4oyKhjnhVK5.png"
>   ],
>   "elements": [
>     {
>       "frontal_image_url": "https://v3b.fal.media/files/b/panda/MQp-ghIqshvMZROKh9lW3.png",
>       "reference_image_urls": [
>         "https://v3b.fal.media/files/b/kangaroo/YMpmQkYt9xugpOTQyZW0O.png",
>         "https://v3b.fal.media/files/b/zebra/d6ywajNyJ6bnpa_xBue-K.png"
>       ]
>     }
>   ]
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
>     "file_size": 7533071,
>     "url": "https://v3b.fal.media/files/b/0a86603b/YAlbB2535l07BTy1wpDeI_output.mp4"
>   }
> }
> ```

> [tab] Reference
>
> **Endpoint:** `POST https://fal.run/fal-ai/kling-video/o1/standard/video-to-video/reference`
> **Endpoint ID:** `fal-ai/kling-video/o1/standard/video-to-video/reference`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/o1/standard/video-to-video/reference/playground)
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
>     "fal-ai/kling-video/o1/standard/video-to-video/reference",
>     arguments={
>         "prompt": "Based on @Video1, generate the next shot. keep the style of the video",
>         "video_url": "https://v3b.fal.media/files/b/panda/oVdiICFXY03Vbam-08Aj8_output.mp4"
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
> const result = await fal.subscribe("fal-ai/kling-video/o1/standard/video-to-video/reference", {
>   input: {
>       prompt: "Based on @Video1, generate the next shot. keep the style of the video",
>       video_url: "https://v3b.fal.media/files/b/panda/oVdiICFXY03Vbam-08Aj8_output.mp4"
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
>   --url https://fal.run/fal-ai/kling-video/o1/standard/video-to-video/reference \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Based on @Video1, generate the next shot. keep the style of the video",
>   "video_url": "https://v3b.fal.media/files/b/panda/oVdiICFXY03Vbam-08Aj8_output.mp4"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> Use @Element1, @Element2 to reference elements and @Image1, @Image2 to reference images in order.
>
> **video\_url** `string` *required*
>
> Reference video URL. Only .mp4/.mov formats supported, 3-10 seconds duration, 720-2160px resolution, max 200MB.
>
> Max file size: 200.0MB, Min width: 720px, Min height: 720px, Max width: 2160px, Max height: 2160px, Min duration: 3.0s, Max duration: 10.05s, Min FPS: 24.0, Max FPS: 60.0, Timeout: 30.0s
>
> **keep\_audio** `boolean` (default: false)
>
> Whether to keep the original audio from the video.
>
> **image\_urls** `list\<string\>`
>
> Reference images for style/appearance. Reference in prompt as @Image1, @Image2, etc. Maximum 4 total (elements + reference images) when using video.
>
> **elements** `list\<OmniVideoElementInput\>`
>
> Elements (characters/objects) to include. Reference in prompt as @Element1, @Element2, etc. Maximum 4 total (elements + reference images) when using video.
>
> **aspect\_ratio** `AspectRatioEnum` (default: auto)
>
> The aspect ratio of the generated video frame. If 'auto', the aspect ratio will be determined automatically based on the input video, and the closest aspect ratio to the input video will be used. Default value: `"auto"`
>
> Possible values: `auto`, `16:9`, `9:16`, `1:1`
>
> **duration** `DurationEnum` (default: 5)
>
> Video duration in seconds. Default value: `"5"`
>
> Possible values: `3`, `4`, `5`, `6`, `7`, `8`, `9`, `10`
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
>   "prompt": "Based on @Video1, generate the next shot. keep the style of the video",
>   "video_url": "https://v3b.fal.media/files/b/panda/oVdiICFXY03Vbam-08Aj8_output.mp4",
>   "keep_audio": false,
>   "image_urls": [],
>   "elements": [
>     {
>       "frontal_image_url": "https://v3b.fal.media/files/b/panda/MQp-ghIqshvMZROKh9lW3.png",
>       "reference_image_urls": [
>         "https://v3b.fal.media/files/b/kangaroo/YMpmQkYt9xugpOTQyZW0O.png",
>         "https://v3b.fal.media/files/b/zebra/d6ywajNyJ6bnpa_xBue-K.png"
>       ]
>     }
>   ],
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
>     "file_size": 28472159,
>     "url": "https://v3b.fal.media/files/b/kangaroo/3n_Lpxm_SjK5NYyBobRdS_output.mp4"
>   }
> }
> ```

## Related

* [Kling O1 First Frame Last Frame to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o1-first-frame-last-frame-to-video) — Video Generation
* [Kling O1 Edit Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o1-edit-video) — Video Generation
* [Kling O1 Reference Image to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o1-reference-image-to-video) — Video Generation
* [Kling O1 Reference Video to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o1-reference-video-to-video) — Video Generation

## Limitations

* `aspect_ratio` restricted to: `auto`, `16:9`, `9:16`, `1:1`
* `duration` restricted to: `3`, `4`, `5`, `6`, `7`, `8`, `9`, `10`
