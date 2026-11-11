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

# Sora 2 Image To Video API

> API reference for Sora 2 Image To Video. Image-to-video endpoint for Sora 2, OpenAI's state-of-the-art video model capable of creating richly detailed, dynamic clips with audio from natural language o

> [tab] Image To Video
>
> **Endpoint:** `POST https://fal.run/fal-ai/sora-2/image-to-video`
> **Endpoint ID:** `fal-ai/sora-2/image-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/sora-2/image-to-video/playground)
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
>     "fal-ai/sora-2/image-to-video",
>     arguments={
>         "prompt": "Front-facing 'invisible' action-cam on a skydiver in freefall above bright clouds; camera locked on his face. He speaks over the wind with clear lipsync: 'This is insanely fun! You've got to try it—book a tandem and go!' Natural wind roar, voice close-mic'd and slightly compressed so it's intelligible. Midday sun, goggles and jumpsuit flutter, altimeter visible, parachute rig on shoulders. Energetic but stable framing with subtle shake; brief horizon roll. End on first tug of canopy and wind noise dropping.",
>         "image_url": "https://storage.googleapis.com/falserverless/example_inputs/sora-2-i2v-input.png"
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
> const result = await fal.subscribe("fal-ai/sora-2/image-to-video", {
>   input: {
>       prompt: "Front-facing 'invisible' action-cam on a skydiver in freefall above bright clouds; camera locked on his face. He speaks over the wind with clear lipsync: 'This is insanely fun! You've got to try it—book a tandem and go!' Natural wind roar, voice close-mic'd and slightly compressed so it's intelligible. Midday sun, goggles and jumpsuit flutter, altimeter visible, parachute rig on shoulders. Energetic but stable framing with subtle shake; brief horizon roll. End on first tug of canopy and wind noise dropping.",
>       image_url: "https://storage.googleapis.com/falserverless/example_inputs/sora-2-i2v-input.png"
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
>   --url https://fal.run/fal-ai/sora-2/image-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Front-facing '\''invisible'\'' action-cam on a skydiver in freefall above bright clouds; camera locked on his face. He speaks over the wind with clear lipsync: '\''This is insanely fun! You'\''ve got to try it—book a tandem and go!'\'' Natural wind roar, voice close-mic'\''d and slightly compressed so it'\''s intelligible. Midday sun, goggles and jumpsuit flutter, altimeter visible, parachute rig on shoulders. Energetic but stable framing with subtle shake; brief horizon roll. End on first tug of canopy and wind noise dropping.",
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/sora-2-i2v-input.png"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The text prompt describing the video you want to generate
>
> **resolution** `ResolutionEnum` (default: auto)
>
> The resolution of the generated video Default value: `"auto"`
>
> Possible values: `auto`, `720p`
>
> **aspect\_ratio** `AspectRatioEnum` (default: auto)
>
> The aspect ratio of the generated video Default value: `"auto"`
>
> Possible values: `auto`, `9:16`, `16:9`
>
> **duration** `DurationEnum` (default: 4)
>
> Duration of the generated video in seconds Default value: `"4"`
>
> Possible values: `4`, `8`, `12`, `16`, `20`
>
> **delete\_video** `boolean` (default: true)
>
> Whether to delete the video after generation for privacy reasons. If True, the video cannot be used for remixing and will be permanently deleted. Default value: `true`
>
> **model** `ModelEnum` (default: sora-2)
>
> The model to use for the generation. When the default model is selected, the latest snapshot of the model will be used - otherwise, select a specific snapshot of the model. Default value: `"sora-2"`
>
> Possible values: `sora-2`, `sora-2-2025-12-08`, `sora-2-2025-10-06`
>
> **detect\_and\_block\_ip** `boolean` (default: false)
>
> If enabled, the prompt (and image for image-to-video) will be checked for known intellectual property references and the request will be blocked if any are detected.
>
> **character\_ids** `list\<string\>`
>
> Up to two character IDs (from create-character) to use in the video. Refer to characters by name in the prompt. When set, only the OpenAI provider is used.
>
> **image\_url** `string` *required*
>
> The URL of the image to use as the first frame
>
> ### Output Schema
>
> **video** `VideoFile` *required*
>
> The generated video
>
> **video\_id** `string` *required*
>
> The ID of the generated video
>
> **thumbnail** `ImageFile`
>
> Thumbnail image for the video
>
> **spritesheet** `ImageFile`
>
> Spritesheet image for the video
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "Front-facing 'invisible' action-cam on a skydiver in freefall above bright clouds; camera locked on his face. He speaks over the wind with clear lipsync: 'This is insanely fun! You've got to try it—book a tandem and go!' Natural wind roar, voice close-mic'd and slightly compressed so it's intelligible. Midday sun, goggles and jumpsuit flutter, altimeter visible, parachute rig on shoulders. Energetic but stable framing with subtle shake; brief horizon roll. End on first tug of canopy and wind noise dropping.",
>   "resolution": "auto",
>   "aspect_ratio": "auto",
>   "duration": 4,
>   "delete_video": true,
>   "model": "sora-2",
>   "detect_and_block_ip": false,
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/sora-2-i2v-input.png"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "content_type": "video/mp4",
>     "url": "https://storage.googleapis.com/falserverless/example_outputs/sora_2_i2v_output.mp4"
>   },
>   "video_id": "video_123"
> }
> ```

> [tab] Pro
>
> **Endpoint:** `POST https://fal.run/fal-ai/sora-2/image-to-video/pro`
> **Endpoint ID:** `fal-ai/sora-2/image-to-video/pro`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/sora-2/image-to-video/pro/playground)
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
>     "fal-ai/sora-2/image-to-video/pro",
>     arguments={
>         "prompt": "Front-facing 'invisible' action-cam on a skydiver in freefall above bright clouds; camera locked on his face. He speaks over the wind with clear lipsync: 'This is insanely fun! You've got to try it—book a tandem and go!' Natural wind roar, voice close-mic'd and slightly compressed so it's intelligible. Midday sun, goggles and jumpsuit flutter, altimeter visible, parachute rig on shoulders. Energetic but stable framing with subtle shake; brief horizon roll. End on first tug of canopy and wind noise dropping.",
>         "image_url": "https://storage.googleapis.com/falserverless/example_inputs/sora-2-i2v-input.png"
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
> const result = await fal.subscribe("fal-ai/sora-2/image-to-video/pro", {
>   input: {
>       prompt: "Front-facing 'invisible' action-cam on a skydiver in freefall above bright clouds; camera locked on his face. He speaks over the wind with clear lipsync: 'This is insanely fun! You've got to try it—book a tandem and go!' Natural wind roar, voice close-mic'd and slightly compressed so it's intelligible. Midday sun, goggles and jumpsuit flutter, altimeter visible, parachute rig on shoulders. Energetic but stable framing with subtle shake; brief horizon roll. End on first tug of canopy and wind noise dropping.",
>       image_url: "https://storage.googleapis.com/falserverless/example_inputs/sora-2-i2v-input.png"
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
>   --url https://fal.run/fal-ai/sora-2/image-to-video/pro \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Front-facing '\''invisible'\'' action-cam on a skydiver in freefall above bright clouds; camera locked on his face. He speaks over the wind with clear lipsync: '\''This is insanely fun! You'\''ve got to try it—book a tandem and go!'\'' Natural wind roar, voice close-mic'\''d and slightly compressed so it'\''s intelligible. Midday sun, goggles and jumpsuit flutter, altimeter visible, parachute rig on shoulders. Energetic but stable framing with subtle shake; brief horizon roll. End on first tug of canopy and wind noise dropping.",
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/sora-2-i2v-input.png"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The text prompt describing the video you want to generate
>
> **resolution** `ResolutionEnum` (default: auto)
>
> The resolution of the generated video Default value: `"auto"`
>
> Possible values: `auto`, `720p`, `1080p`, `true_1080p`
>
> **aspect\_ratio** `AspectRatioEnum` (default: auto)
>
> The aspect ratio of the generated video Default value: `"auto"`
>
> Possible values: `auto`, `9:16`, `16:9`
>
> **duration** `DurationEnum` (default: 4)
>
> Duration of the generated video in seconds Default value: `"4"`
>
> Possible values: `4`, `8`, `12`, `16`, `20`
>
> **delete\_video** `boolean` (default: true)
>
> Whether to delete the video after generation for privacy reasons. If True, the video cannot be used for remixing and will be permanently deleted. Default value: `true`
>
> **detect\_and\_block\_ip** `boolean` (default: false)
>
> If enabled, the prompt (and image for image-to-video) will be checked for known intellectual property references and the request will be blocked if any are detected.
>
> **character\_ids** `list\<string\>`
>
> Up to two character IDs (from create-character) to use in the video. Refer to characters by name in the prompt. When set, only the OpenAI provider is used.
>
> **image\_url** `string` *required*
>
> The URL of the image to use as the first frame
>
> ### Output Schema
>
> **video** `VideoFile` *required*
>
> The generated video
>
> **video\_id** `string` *required*
>
> The ID of the generated video
>
> **thumbnail** `ImageFile`
>
> Thumbnail image for the video
>
> **spritesheet** `ImageFile`
>
> Spritesheet image for the video
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "Front-facing 'invisible' action-cam on a skydiver in freefall above bright clouds; camera locked on his face. He speaks over the wind with clear lipsync: 'This is insanely fun! You've got to try it—book a tandem and go!' Natural wind roar, voice close-mic'd and slightly compressed so it's intelligible. Midday sun, goggles and jumpsuit flutter, altimeter visible, parachute rig on shoulders. Energetic but stable framing with subtle shake; brief horizon roll. End on first tug of canopy and wind noise dropping.",
>   "resolution": "auto",
>   "aspect_ratio": "auto",
>   "duration": 4,
>   "delete_video": true,
>   "detect_and_block_ip": false,
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/sora-2-i2v-input.png"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "content_type": "video/mp4",
>     "url": "https://storage.googleapis.com/falserverless/example_outputs/sora-2-pro-i2v-output.mp4"
>   },
>   "video_id": "video_123"
> }
> ```

## Related

* [Sora 2](https://fal.ai/docs/model-api-reference/video-generation-api/sora-2) — Video Generation

## Limitations

* `resolution` restricted to: `auto`, `720p`
* `aspect_ratio` restricted to: `auto`, `9:16`, `16:9`
* `duration` restricted to: `4`, `8`, `12`, `16`, `20`
* `model` restricted to: `sora-2`, `sora-2-2025-12-08`, `sora-2-2025-10-06`
* `resolution` restricted to: `auto`, `720p`, `1080p`, `true_1080p`
