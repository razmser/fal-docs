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

# Xai Grok Imagine Video API

> API reference for Xai Grok Imagine Video. Generate videos from images with audio using xAI's Grok Imagine Video model.

> [tab] Image To Video
>
> **Endpoint:** `POST https://fal.run/xai/grok-imagine-video/image-to-video`
> **Endpoint ID:** `xai/grok-imagine-video/image-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/xai/grok-imagine-video/image-to-video/playground)
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
>     "xai/grok-imagine-video/image-to-video",
>     arguments={
>         "prompt": "Medieval knight in ornate armor walking through a mystical forest, bioluminescent plants pulsing with light, ancient stone ruins overgrown with glowing vines, over-the-shoulder camera, dark fantasy aesthetic, volumetric fog and Lumen lighting",
>         "image_url": "https://v3b.fal.media/files/b/0a8b90e0/BFLE9VDlZqsryU-UA3BoD_image_004.png"
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
> const result = await fal.subscribe("xai/grok-imagine-video/image-to-video", {
>   input: {
>       prompt: "Medieval knight in ornate armor walking through a mystical forest, bioluminescent plants pulsing with light, ancient stone ruins overgrown with glowing vines, over-the-shoulder camera, dark fantasy aesthetic, volumetric fog and Lumen lighting",
>       image_url: "https://v3b.fal.media/files/b/0a8b90e0/BFLE9VDlZqsryU-UA3BoD_image_004.png"
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
>   --url https://fal.run/xai/grok-imagine-video/image-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Medieval knight in ornate armor walking through a mystical forest, bioluminescent plants pulsing with light, ancient stone ruins overgrown with glowing vines, over-the-shoulder camera, dark fantasy aesthetic, volumetric fog and Lumen lighting",
>   "image_url": "https://v3b.fal.media/files/b/0a8b90e0/BFLE9VDlZqsryU-UA3BoD_image_004.png"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> Text description of desired changes or motion in the video.
>
> **duration** `integer` (default: 6)
>
> Video duration in seconds. Default value: `6`
>
> Range: `1` to `15`
>
> **aspect\_ratio** `Enum` (default: auto)
>
> Aspect ratio of the generated video. Default value: `auto`
>
> Possible values: `auto`, `16:9`, `4:3`, `3:2`, `1:1`, `2:3`, `3:4`, `9:16`
>
> **resolution** `ResolutionEnum` (default: 720p)
>
> Resolution of the output video. Default value: `"720p"`
>
> Possible values: `480p`, `720p`
>
> **image\_url** `string` *required*
>
> URL of the input image for video generation.
>
> ### Output Schema
>
> **video** `VideoFile` *required*
>
> The generated video.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "Medieval knight in ornate armor walking through a mystical forest, bioluminescent plants pulsing with light, ancient stone ruins overgrown with glowing vines, over-the-shoulder camera, dark fantasy aesthetic, volumetric fog and Lumen lighting",
>   "duration": 6,
>   "aspect_ratio": "auto",
>   "resolution": "720p",
>   "image_url": "https://v3b.fal.media/files/b/0a8b90e0/BFLE9VDlZqsryU-UA3BoD_image_004.png"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "content_type": "video/mp4",
>     "duration": 6.041667,
>     "file_name": "0Ci1dviuSnEyUZzBUq-_5_nu7MrAAa.mp4",
>     "fps": 24,
>     "height": 720,
>     "num_frames": 145,
>     "url": "https://v3b.fal.media/files/b/0a8b90e0/0Ci1dviuSnEyUZzBUq-_5_nu7MrAAa.mp4",
>     "width": 1280
>   }
> }
> ```

> [tab] Text To Video
>
> **Endpoint:** `POST https://fal.run/xai/grok-imagine-video/text-to-video`
> **Endpoint ID:** `xai/grok-imagine-video/text-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/xai/grok-imagine-video/text-to-video/playground)
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
>     "xai/grok-imagine-video/text-to-video",
>     arguments={
>         "prompt": "Anime schoolgirl bursting out of house door, cherry blossoms blowing, morning light, speed lines indicating rush, chibi-ready expressions, classic shojo aesthetic, vibrant colors"
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
> const result = await fal.subscribe("xai/grok-imagine-video/text-to-video", {
>   input: {
>       prompt: "Anime schoolgirl bursting out of house door, cherry blossoms blowing, morning light, speed lines indicating rush, chibi-ready expressions, classic shojo aesthetic, vibrant colors"
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
>   --url https://fal.run/xai/grok-imagine-video/text-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Anime schoolgirl bursting out of house door, cherry blossoms blowing, morning light, speed lines indicating rush, chibi-ready expressions, classic shojo aesthetic, vibrant colors"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> Text description of the desired video.
>
> **duration** `integer` (default: 6)
>
> Video duration in seconds. Default value: `6`
>
> Range: `1` to `15`
>
> **aspect\_ratio** `AspectRatioEnum` (default: 16:9)
>
> Aspect ratio of the generated video. Default value: `"16:9"`
>
> Possible values: `16:9`, `4:3`, `3:2`, `1:1`, `2:3`, `3:4`, `9:16`
>
> **resolution** `ResolutionEnum` (default: 720p)
>
> Resolution of the output video. Default value: `"720p"`
>
> Possible values: `480p`, `720p`
>
> ### Output Schema
>
> **video** `VideoFile` *required*
>
> The generated video.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "Anime schoolgirl bursting out of house door, cherry blossoms blowing, morning light, speed lines indicating rush, chibi-ready expressions, classic shojo aesthetic, vibrant colors",
>   "duration": 6,
>   "aspect_ratio": "16:9",
>   "resolution": "720p"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "content_type": "video/mp4",
>     "duration": 6.041667,
>     "file_name": "RUAbFYlssdqnbjNLmE8qP_IX7BNYGP.mp4",
>     "fps": 24,
>     "height": 720,
>     "num_frames": 145,
>     "url": "https://v3b.fal.media/files/b/0a8b90e4/RUAbFYlssdqnbjNLmE8qP_IX7BNYGP.mp4",
>     "width": 1280
>   }
> }
> ```

> [tab] Reference To Video
>
> **Endpoint:** `POST https://fal.run/xai/grok-imagine-video/reference-to-video`
> **Endpoint ID:** `xai/grok-imagine-video/reference-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/xai/grok-imagine-video/reference-to-video/playground)
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
>     "xai/grok-imagine-video/reference-to-video",
>     arguments={
>         "prompt": "A @Image1 running through a sunlit meadow, cinematic slow motion",
>         "reference_image_urls": [
>             "https://v3b.fal.media/files/b/0a8b90e0/BFLE9VDlZqsryU-UA3BoD_image_004.png"
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
> const result = await fal.subscribe("xai/grok-imagine-video/reference-to-video", {
>   input: {
>       prompt: "A @Image1 running through a sunlit meadow, cinematic slow motion",
>       reference_image_urls: [
>         "https://v3b.fal.media/files/b/0a8b90e0/BFLE9VDlZqsryU-UA3BoD_image_004.png"
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
>   --url https://fal.run/xai/grok-imagine-video/reference-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A @Image1 running through a sunlit meadow, cinematic slow motion",
>   "reference_image_urls": [
>     "https://v3b.fal.media/files/b/0a8b90e0/BFLE9VDlZqsryU-UA3BoD_image_004.png"
>   ]
> }'
> ```
>
> ## Examples
>
> > This man walking in this road between columns
>
> <video src="https://v3b.fal.media/files/b/0a937b3e/SCRymQFjmgeuvDXWJkCt6_eEjGlXhW.mp4" controls width="100%" />
>
> > This man walking in this road between columns
>
> <video src="https://v3b.fal.media/files/b/0a937b3e/SCRymQFjmgeuvDXWJkCt6_eEjGlXhW.mp4" controls width="100%" />
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> Text prompt describing the video to generate. Use @Image1, @Image2, etc. to reference specific images from reference\_image\_urls in order.
>
> **reference\_image\_urls** `list\<string\>` *required*
>
> One or more reference image URLs to guide the video generation as style and content references. Reference in prompt as @Image1, @Image2, etc. Maximum 7 images.
>
> **duration** `integer` (default: 8)
>
> Video duration in seconds. Default value: `8`
>
> Range: `1` to `10`
>
> **aspect\_ratio** `AspectRatioEnum` (default: 16:9)
>
> Aspect ratio of the generated video. Default value: `"16:9"`
>
> Possible values: `16:9`, `4:3`, `3:2`, `1:1`, `2:3`, `3:4`, `9:16`
>
> **resolution** `ResolutionEnum` (default: 480p)
>
> Resolution of the output video. Default value: `"480p"`
>
> Possible values: `480p`, `720p`
>
> ### Output Schema
>
> **video** `VideoFile` *required*
>
> The generated video.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "A @Image1 running through a sunlit meadow, cinematic slow motion",
>   "reference_image_urls": [
>     "https://v3b.fal.media/files/b/0a8b90e0/BFLE9VDlZqsryU-UA3BoD_image_004.png"
>   ],
>   "duration": 8,
>   "aspect_ratio": "16:9",
>   "resolution": "480p"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "content_type": "video/mp4",
>     "duration": 8,
>     "file_name": "r2v_output.mp4",
>     "fps": 24,
>     "height": 720,
>     "num_frames": 192,
>     "url": "https://v3b.fal.media/files/b/0a8b90e4/r2v_output.mp4",
>     "width": 1280
>   }
> }
> ```

> [tab] Edit Video
>
> **Endpoint:** `POST https://fal.run/xai/grok-imagine-video/edit-video`
> **Endpoint ID:** `xai/grok-imagine-video/edit-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/xai/grok-imagine-video/edit-video/playground)
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
>     "xai/grok-imagine-video/edit-video",
>     arguments={
>         "prompt": "Colorize the video",
>         "video_url": "https://v3b.fal.media/files/b/0a8b9112/V5Z_NIPE3ppMDWivNo6_q_video_019.mp4"
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
> const result = await fal.subscribe("xai/grok-imagine-video/edit-video", {
>   input: {
>       prompt: "Colorize the video",
>       video_url: "https://v3b.fal.media/files/b/0a8b9112/V5Z_NIPE3ppMDWivNo6_q_video_019.mp4"
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
>   --url https://fal.run/xai/grok-imagine-video/edit-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Colorize the video",
>   "video_url": "https://v3b.fal.media/files/b/0a8b9112/V5Z_NIPE3ppMDWivNo6_q_video_019.mp4"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> Text description of the desired edit.
>
> **video\_url** `string` *required*
>
> URL of the input video to edit. The video will be resized to a maximum area of 854x480 pixels and truncated to 8 seconds.
>
> **resolution** `ResolutionEnum` (default: auto)
>
> Resolution of the output video. Default value: `"auto"`
>
> Possible values: `auto`, `480p`, `720p`
>
> ### Output Schema
>
> **video** `VideoFile` *required*
>
> The generated video.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "Colorize the video",
>   "video_url": "https://v3b.fal.media/files/b/0a8b9112/V5Z_NIPE3ppMDWivNo6_q_video_019.mp4",
>   "resolution": "auto"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "content_type": "video/mp4",
>     "duration": 5.041667,
>     "file_name": "EuDrZuQTW9m1phBXOsauz_EpJH3s8X.mp4",
>     "fps": 24,
>     "height": 720,
>     "num_frames": 121,
>     "url": "https://v3b.fal.media/files/b/0a8b9113/EuDrZuQTW9m1phBXOsauz_EpJH3s8X.mp4",
>     "width": 1280
>   }
> }
> ```

> [tab] Extend Video
>
> **Endpoint:** `POST https://fal.run/xai/grok-imagine-video/extend-video`
> **Endpoint ID:** `xai/grok-imagine-video/extend-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/xai/grok-imagine-video/extend-video/playground)
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
>     "xai/grok-imagine-video/extend-video",
>     arguments={
>         "prompt": "The camera slowly zooms out to reveal the city skyline at sunset",
>         "video_url": "https://v3b.fal.media/files/b/0a8b9112/V5Z_NIPE3ppMDWivNo6_q_video_019.mp4"
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
> const result = await fal.subscribe("xai/grok-imagine-video/extend-video", {
>   input: {
>       prompt: "The camera slowly zooms out to reveal the city skyline at sunset",
>       video_url: "https://v3b.fal.media/files/b/0a8b9112/V5Z_NIPE3ppMDWivNo6_q_video_019.mp4"
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
>   --url https://fal.run/xai/grok-imagine-video/extend-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "The camera slowly zooms out to reveal the city skyline at sunset",
>   "video_url": "https://v3b.fal.media/files/b/0a8b9112/V5Z_NIPE3ppMDWivNo6_q_video_019.mp4"
> }'
> ```
>
> ## Examples
>
> > Clouds timelapse there are mountains behind
>
> <video src="https://v3b.fal.media/files/b/0a937b62/EGsarDTK78nSVKlMlPli1_TyYKmwA0.mp4" controls width="100%" />
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> Text description of what should happen next in the video.
>
> **video\_url** `string` *required*
>
> URL of the source video to extend. Must be MP4 format (H.264, H.265, or AV1 codec), 2-15 seconds long.
>
> **duration** `integer` (default: 6)
>
> Length of the extension in seconds. Default value: `6`
>
> Range: `2` to `10`
>
> ### Output Schema
>
> **video** `VideoFile` *required*
>
> The extended video (original + extension stitched together).
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "The camera slowly zooms out to reveal the city skyline at sunset",
>   "video_url": "https://v3b.fal.media/files/b/0a8b9112/V5Z_NIPE3ppMDWivNo6_q_video_019.mp4",
>   "duration": 6
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "content_type": "video/mp4",
>     "duration": 16,
>     "file_name": "extended_video.mp4",
>     "fps": 24,
>     "height": 720,
>     "num_frames": 384,
>     "url": "https://v3b.fal.media/files/b/0a8b9113/extended_video.mp4",
>     "width": 1280
>   }
> }
> ```

## Related

* [Grok Imagine Video](https://fal.ai/docs/model-api-reference/video-generation-api/grok-imagine-video) — Video Generation
* [Grok Imagine Reference to Video](https://fal.ai/docs/model-api-reference/video-generation-api/grok-imagine-reference-to-video) — Video Generation
* [Grok Imagine Extend Video](https://fal.ai/docs/model-api-reference/video-generation-api/grok-imagine-extend-video) — Video Generation

## Limitations

* `duration` range: 1 to 15
* `aspect_ratio` restricted to: `auto`, `16:9`, `4:3`, `3:2`, `1:1`, `2:3`, `3:4`, `9:16`
* `resolution` restricted to: `480p`, `720p`
* `aspect_ratio` restricted to: `16:9`, `4:3`, `3:2`, `1:1`, `2:3`, `3:4`, `9:16`
* `duration` range: 1 to 10
* `resolution` restricted to: `auto`, `480p`, `720p`
* `duration` range: 2 to 10
