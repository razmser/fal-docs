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

# Kling Video V3 Pro API

> API reference for Kling Video V3 Pro. Kling 3.0 Pro: Top-tier text-to-video with cinematic visuals, fluid motion, and native audio generation, with multi-shot support.

> [tab] Text To Video
>
> **Endpoint:** `POST https://fal.run/fal-ai/kling-video/v3/pro/text-to-video`
> **Endpoint ID:** `fal-ai/kling-video/v3/pro/text-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/v3/pro/text-to-video/playground)
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
>     "fal-ai/kling-video/v3/pro/text-to-video",
>     arguments={},
>     with_logs=True,
>     on_queue_update=on_queue_update,
> )
> print(result)
> ```
>
> ```javascript title="JavaScript" theme={null}
> import { fal } from "@fal-ai/client";
>
> const result = await fal.subscribe("fal-ai/kling-video/v3/pro/text-to-video", {
>   input: {},
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
>   --url https://fal.run/fal-ai/kling-video/v3/pro/text-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{}'
> ```
>
> ### Input Schema
>
> **prompt** `string`
>
> Text prompt for video generation. Either prompt or multi\_prompt must be provided, but not both.
>
> **duration** `DurationEnum` (default: 5)
>
> The duration of the generated video in seconds Default value: `"5"`
>
> Possible values: `3`, `4`, `5`, `6`, `7`, `8`, `9`, `10`, `11`, `12`, `13`, `14`, `15`
>
> **multi\_prompt** `list\<KlingV3MultiPromptElement\>`
>
> List of prompts for multi-shot video generation. If provided, overrides the single prompt and divides the video into multiple shots with specified prompts and durations.
>
> **generate\_audio** `boolean` (default: true)
>
> Whether to generate native audio for the video. Supports Chinese and English voice output. Other languages are automatically translated to English. For English speech, use lowercase letters; for acronyms or proper nouns, use uppercase. Default value: `true`
>
> **shot\_type** `ShotTypeEnum` (default: customize)
>
> The type of multi-shot video generation Default value: `"customize"`
>
> Possible values: `customize`, `intelligent`
>
> **aspect\_ratio** `AspectRatioEnum` (default: 16:9)
>
> The aspect ratio of the generated video frame Default value: `"16:9"`
>
> Possible values: `16:9`, `9:16`, `1:1`
>
> **negative\_prompt** `string` (default: blur, distort, and low quality)
>
> Default value: `"blur, distort, and low quality"`
>
> **cfg\_scale** `float` (default: 0.5)
>
> The CFG (Classifier Free Guidance) scale is a measure of how close you want
> the model to stick to your prompt. Default value: `0.5`
>
> Range: `0` to `1`
>
> ### Output Schema
>
> **video** `File` *required*
>
> The generated video
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "Close-up of glowing fireflies dancing in a dark forest at twilight. Soft bioluminescent particles float through the air. Shallow depth of field, bokeh lights in background. Magical atmosphere, gentle movement.",
>   "duration": "5",
>   "multi_prompt": null,
>   "generate_audio": true,
>   "shot_type": "customize",
>   "aspect_ratio": "16:9",
>   "negative_prompt": "blur, distort, and low quality",
>   "cfg_scale": 0.5
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
>     "file_size": 8062911,
>     "url": "https://storage.googleapis.com/falserverless/example_outputs/kling-v3/pro-t2v/out.mp4"
>   }
> }
> ```

> [tab] Image To Video
>
> **Endpoint:** `POST https://fal.run/fal-ai/kling-video/v3/pro/image-to-video`
> **Endpoint ID:** `fal-ai/kling-video/v3/pro/image-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/v3/pro/image-to-video/playground)
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
>     "fal-ai/kling-video/v3/pro/image-to-video",
>     arguments={
>         "start_image_url": "https://storage.googleapis.com/falserverless/example_inputs/kling-v3/pro-i2v/start_image.png"
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
> const result = await fal.subscribe("fal-ai/kling-video/v3/pro/image-to-video", {
>   input: {
>       start_image_url: "https://storage.googleapis.com/falserverless/example_inputs/kling-v3/pro-i2v/start_image.png"
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
>   --url https://fal.run/fal-ai/kling-video/v3/pro/image-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "start_image_url": "https://storage.googleapis.com/falserverless/example_inputs/kling-v3/pro-i2v/start_image.png"
> }'
> ```
>
> ## Examples
>
> <video src="https://v3b.fal.media/files/b/0a9270c0/M0OE5-o3n7Pj85CWWpGt2_output.mp4" controls width="100%" />
>
> > Motorcycle racing through traffic at dangerous speeds
>
> <video src="https://v3b.fal.media/files/b/0a8d2087/7Ps8bBagTdb-frW9zj1BT_output.mp4" controls width="100%" />
>
> > Motorcycle racing through traffic at dangerous speeds
>
> <video src="https://v3b.fal.media/files/b/0a920eb0/5ecOVwqw-5zFU3_Py4xCK_output.mp4" controls width="100%" />
>
> ### Input Schema
>
> **prompt** `string`
>
> Text prompt for video generation. Either prompt or multi\_prompt must be provided, but not both.
>
> **multi\_prompt** `list\<KlingV3MultiPromptElement\>`
>
> List of prompts for multi-shot video generation. If provided, divides the video into multiple shots.
>
> **start\_image\_url** `string` *required*
>
> URL of the image to be used for the video
>
> **duration** `DurationEnum` (default: 5)
>
> The duration of the generated video in seconds Default value: `"5"`
>
> Possible values: `3`, `4`, `5`, `6`, `7`, `8`, `9`, `10`, `11`, `12`, `13`, `14`, `15`
>
> **generate\_audio** `boolean` (default: true)
>
> Whether to generate native audio for the video. Supports Chinese and English voice output. Other languages are automatically translated to English. For English speech, use lowercase letters; for acronyms or proper nouns, use uppercase. Default value: `true`
>
> **end\_image\_url** `string`
>
> URL of the image to be used for the end of the video
>
> **elements** `list\<KlingV3ComboElementInput\>`
>
> Elements (characters/objects) to include in the video. Each example can either be an image set (frontal + reference images) or a video. Reference in prompt as @Element1, @Element2, etc.
>
> **shot\_type** `string` (default: customize)
>
> The type of multi-shot video generation. Required when multi\_prompt is provided. Default value: `"customize"`
>
> **negative\_prompt** `string` (default: blur, distort, and low quality)
>
> Default value: `"blur, distort, and low quality"`
>
> **cfg\_scale** `float` (default: 0.5)
>
> The CFG (Classifier Free Guidance) scale is a measure of how close you want
> the model to stick to your prompt. Default value: `0.5`
>
> Range: `0` to `1`
>
> ### Output Schema
>
> **video** `File` *required*
>
> The generated video
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "The craftsman slowly examines the bowl, turning it gently in his weathered hands. His eyes reflect years of wisdom. Subtle smile forms on his face. Dust particles drift in warm light. Breathing motion, blinking eyes.",
>   "multi_prompt": null,
>   "start_image_url": "https://storage.googleapis.com/falserverless/example_inputs/kling-v3/pro-i2v/start_image.png",
>   "duration": "12",
>   "generate_audio": true,
>   "elements": [
>     {
>       "frontal_image_url": "https://v3b.fal.media/files/b/0a8cfd5f/-kZL-ha3Iuelku5IHXC-A_glasses.png",
>       "reference_image_urls": [
>         "https://v3b.fal.media/files/b/0a8cfd62/psPCmzrD1y9vDgdyNfKAL_glasses_back.png"
>       ]
>     },
>     {
>       "video_url": "https://v3b.fal.media/files/b/0a8cfd66/b03SOiQvKLlFx_jqdNZ9z_child_video.mp4"
>     }
>   ],
>   "shot_type": "customize",
>   "negative_prompt": "blur, distort, and low quality",
>   "cfg_scale": 0.5
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "content_type": "video/mp4",
>     "file_name": "out.mp4",
>     "file_size": 8431922,
>     "url": "https://storage.googleapis.com/falserverless/example_outputs/kling-v3/pro-i2v/out.mp4"
>   }
> }
> ```

> [tab] Motion Control
>
> **Endpoint:** `POST https://fal.run/fal-ai/kling-video/v3/pro/motion-control`
> **Endpoint ID:** `fal-ai/kling-video/v3/pro/motion-control`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/v3/pro/motion-control/playground)
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
>     "fal-ai/kling-video/v3/pro/motion-control",
>     arguments={
>         "image_url": "https://v3b.fal.media/files/b/0a90ffa7/TNErq9yD7ZxGRATjfAqnh_EIgJSN67.png",
>         "video_url": "https://v3b.fal.media/files/b/0a90ff92/hklvF__w53diz6Rve7f5__JuDW2xl0mr6sJ_Kjz3Vxe_vidoeook%20(1)_1.mp4",
>         "character_orientation": "image"
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
> const result = await fal.subscribe("fal-ai/kling-video/v3/pro/motion-control", {
>   input: {
>       image_url: "https://v3b.fal.media/files/b/0a90ffa7/TNErq9yD7ZxGRATjfAqnh_EIgJSN67.png",
>       video_url: "https://v3b.fal.media/files/b/0a90ff92/hklvF__w53diz6Rve7f5__JuDW2xl0mr6sJ_Kjz3Vxe_vidoeook%20(1)_1.mp4",
>       character_orientation: "image"
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
>   --url https://fal.run/fal-ai/kling-video/v3/pro/motion-control \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://v3b.fal.media/files/b/0a90ffa7/TNErq9yD7ZxGRATjfAqnh_EIgJSN67.png",
>   "video_url": "https://v3b.fal.media/files/b/0a90ff92/hklvF__w53diz6Rve7f5__JuDW2xl0mr6sJ_Kjz3Vxe_vidoeook%20(1)_1.mp4",
>   "character_orientation": "image"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string`
>
> **image\_url** `string` *required*
>
> Reference image URL. The characters, backgrounds, and other elements in the generated video are based on this reference image. Characters should have clear body proportions, avoid occlusion, and occupy more than 5% of the image area.
>
> **video\_url** `string` *required*
>
> Reference video URL. The character actions in the generated video will be consistent with this reference video. Should contain a realistic style character with entire body or upper body visible, including head, without obstruction. Duration limit depends on character\_orientation: 10s max for 'image', 30s max for 'video'.
>
> **keep\_original\_sound** `boolean` (default: true)
>
> Whether to keep the original sound from the reference video. Default value: `true`
>
> **character\_orientation** `CharacterOrientationEnum` *required*
>
> Controls whether the output character's orientation matches the reference image or video. 'video': orientation matches reference video - better for complex motions (max 30s). 'image': orientation matches reference image - better for following camera movements (max 10s).
>
> Possible values: `image`, `video`
>
> **elements** `list\<KlingV3ImageElementInput\>`
>
> Optional element for facial consistency binding. Upload a facial element to enhance identity preservation in the generated video. Only 1 element is supported. Reference in prompt as @Element1. Element binding is only supported when character\_orientation is 'video'.
>
> ### Output Schema
>
> **video** `File` *required*
>
> The generated video
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "A man dancing",
>   "image_url": "https://v3b.fal.media/files/b/0a90ffa7/TNErq9yD7ZxGRATjfAqnh_EIgJSN67.png",
>   "video_url": "https://v3b.fal.media/files/b/0a90ff92/hklvF__w53diz6Rve7f5__JuDW2xl0mr6sJ_Kjz3Vxe_vidoeook%20(1)_1.mp4",
>   "keep_original_sound": true,
>   "character_orientation": "image",
>   "elements": null
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "url": "https://v3b.fal.media/files/b/0a90ffb9/CnmmxIvK05VAq4gG8WiEQ_output.mp4"
>   }
> }
> ```

## Related

* [Kling Video v3 Image to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-video-v3-image-to-video) — Video Generation
* [Kling Video v3 Text to Video \[Standard\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-video-v3-text-to-video) — Video Generation
* [Kling Video](https://fal.ai/docs/model-api-reference/video-generation-api/kling-video) — Video Generation

## Limitations

* `shot_type` restricted to: `customize`, `intelligent`
* `aspect_ratio` restricted to: `16:9`, `9:16`, `1:1`
* `cfg_scale` range: 0 to 1
* `character_orientation` restricted to: `image`, `video`
