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

# Veo3.1 Lite API

> API reference for Veo3.1 Lite. Veo 3.1 Lite balances practical utility with professional capabilities, supporting Text-to-Video and Image-to-Video

> [tab] Lite
>
> **Endpoint:** `POST https://fal.run/fal-ai/veo3.1/lite`
> **Endpoint ID:** `fal-ai/veo3.1/lite`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/veo3.1/lite/playground)
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
>     "fal-ai/veo3.1/lite",
>     arguments={
>         "prompt": "A massive blue whale glides through crystal-clear deep ocean water, sunlight rays piercing through the surface above, bioluminescent plankton scattered around, cinematic slow motion"
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
> const result = await fal.subscribe("fal-ai/veo3.1/lite", {
>   input: {
>       prompt: "A massive blue whale glides through crystal-clear deep ocean water, sunlight rays piercing through the surface above, bioluminescent plankton scattered around, cinematic slow motion"
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
>   --url https://fal.run/fal-ai/veo3.1/lite \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A massive blue whale glides through crystal-clear deep ocean water, sunlight rays piercing through the surface above, bioluminescent plankton scattered around, cinematic slow motion"
> }'
> ```
>
> ## Examples
>
> > A ballerina performing an elegant pirouette on a moonlit outdoor stage. She wears a flowing white tutu that catches the silver light. Rose petals swirl around her in slow motion. The background is a dark forest with fireflies. Camera orbits slowly around her as she spins. Ethereal, dreamlike, cinema...
>
> <video src="https://v3b.fal.media/files/b/0a946855/rR-4fWeyUoQgo8g32Y1gy_8263e382d6734cfcb6f9b7160f127065.mp4" controls width="100%" />
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The text prompt describing the video you want to generate
>
> **aspect\_ratio** `AspectRatioEnum` (default: 16:9)
>
> Aspect ratio of the generated video Default value: `"16:9"`
>
> Possible values: `16:9`, `9:16`
>
> **duration** `DurationEnum` (default: 8s)
>
> The duration of the generated video. Default value: `"8s"`
>
> Possible values: `4s`, `6s`, `8s`
>
> **negative\_prompt** `string`
>
> A negative prompt to guide the video generation.
>
> **resolution** `ResolutionEnum` (default: 720p)
>
> The resolution of the generated video. Default value: `"720p"`
>
> Possible values: `720p`, `1080p`
>
> **generate\_audio** `boolean` (default: true)
>
> Whether to generate audio for the video. Default value: `true`
>
> **seed** `integer`
>
> The seed for the random number generator.
>
> **auto\_fix** `boolean` (default: true)
>
> Whether to automatically attempt to fix prompts that fail content policy or other validation checks by rewriting them. Default value: `true`
>
> **safety\_tolerance** `SafetyToleranceEnum` (default: 4)
>
> The safety tolerance level for content moderation. 1 is the most strict (blocks most content), 6 is the least strict. Default value: `"4"`
>
> Possible values: `1`, `2`, `3`, `4`, `5`, `6`
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
>   "prompt": "A massive blue whale glides through crystal-clear deep ocean water, sunlight rays piercing through the surface above, bioluminescent plankton scattered around, cinematic slow motion",
>   "aspect_ratio": "16:9",
>   "duration": "8s",
>   "resolution": "720p",
>   "generate_audio": true,
>   "auto_fix": true,
>   "safety_tolerance": "4"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "url": "https://v3b.fal.media/files/b/0a94683f/r4vLtNmFDi_qpglfEX_q9_0a7258527ac84509a0997639765f5c79.mp4"
>   }
> }
> ```

> [tab] Image To Video
>
> **Endpoint:** `POST https://fal.run/fal-ai/veo3.1/lite/image-to-video`
> **Endpoint ID:** `fal-ai/veo3.1/lite/image-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/veo3.1/lite/image-to-video/playground)
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
>     "fal-ai/veo3.1/lite/image-to-video",
>     arguments={
>         "prompt": "The subject turns to face the camera and smiles warmly.",
>         "image_url": "https://storage.googleapis.com/falserverless/example_inputs/veo3-i2v-input.png"
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
> const result = await fal.subscribe("fal-ai/veo3.1/lite/image-to-video", {
>   input: {
>       prompt: "The subject turns to face the camera and smiles warmly.",
>       image_url: "https://storage.googleapis.com/falserverless/example_inputs/veo3-i2v-input.png"
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
>   --url https://fal.run/fal-ai/veo3.1/lite/image-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "The subject turns to face the camera and smiles warmly.",
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/veo3-i2v-input.png"
> }'
> ```
>
> ## Examples
>
> > Time-lapse of the city transitioning through the day. Shadows shift across buildings, traffic flows in streaks of light, clouds race across the sky. The scene shifts from bright midday to warm golden hour. Smooth hyperlapse feel.
>
> <video src="https://v3b.fal.media/files/b/0a94686f/UtJVZJIY6D9NwIaB3TVwu_738e51572750421fbb5fc3b78029988a.mp4" controls width="100%" />
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The text prompt describing how the image should be animated
>
> **aspect\_ratio** `AspectRatioEnum` (default: auto)
>
> The aspect ratio of the generated video. Default value: `"auto"`
>
> Possible values: `auto`, `16:9`, `9:16`
>
> **duration** `DurationEnum` (default: 8s)
>
> The duration of the generated video. Default value: `"8s"`
>
> Possible values: `4s`, `6s`, `8s`
>
> **negative\_prompt** `string`
>
> A negative prompt to guide the video generation.
>
> **resolution** `ResolutionEnum` (default: 720p)
>
> The resolution of the generated video. Default value: `"720p"`
>
> Possible values: `720p`, `1080p`
>
> **generate\_audio** `boolean` (default: true)
>
> Whether to generate audio for the video. Default value: `true`
>
> **seed** `integer`
>
> The seed for the random number generator.
>
> **auto\_fix** `boolean` (default: false)
>
> Whether to automatically attempt to fix prompts that fail content policy or other validation checks by rewriting them.
>
> **safety\_tolerance** `SafetyToleranceEnum` (default: 4)
>
> The safety tolerance level for content moderation. 1 is the most strict (blocks most content), 6 is the least strict. Default value: `"4"`
>
> Possible values: `1`, `2`, `3`, `4`, `5`, `6`
>
> **image\_url** `string` *required*
>
> URL of the input image to animate. Should be 720p or higher resolution in 16:9 or 9:16 aspect ratio.
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
>   "prompt": "The subject turns to face the camera and smiles warmly.",
>   "aspect_ratio": "auto",
>   "duration": "8s",
>   "resolution": "720p",
>   "generate_audio": true,
>   "auto_fix": false,
>   "safety_tolerance": "4",
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/veo3-i2v-input.png"
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

> [tab] First Last Frame To Video
>
> **Endpoint:** `POST https://fal.run/fal-ai/veo3.1/lite/first-last-frame-to-video`
> **Endpoint ID:** `fal-ai/veo3.1/lite/first-last-frame-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/veo3.1/lite/first-last-frame-to-video/playground)
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
>     "fal-ai/veo3.1/lite/first-last-frame-to-video",
>     arguments={
>         "prompt": "A smooth transition between the two frames with natural motion.",
>         "first_frame_url": "https://storage.googleapis.com/falserverless/example_inputs/veo31-flf2v-input-1.jpeg",
>         "last_frame_url": "https://storage.googleapis.com/falserverless/example_inputs/veo31-flf2v-input-2.jpeg"
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
> const result = await fal.subscribe("fal-ai/veo3.1/lite/first-last-frame-to-video", {
>   input: {
>       prompt: "A smooth transition between the two frames with natural motion.",
>       first_frame_url: "https://storage.googleapis.com/falserverless/example_inputs/veo31-flf2v-input-1.jpeg",
>       last_frame_url: "https://storage.googleapis.com/falserverless/example_inputs/veo31-flf2v-input-2.jpeg"
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
>   --url https://fal.run/fal-ai/veo3.1/lite/first-last-frame-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A smooth transition between the two frames with natural motion.",
>   "first_frame_url": "https://storage.googleapis.com/falserverless/example_inputs/veo31-flf2v-input-1.jpeg",
>   "last_frame_url": "https://storage.googleapis.com/falserverless/example_inputs/veo31-flf2v-input-2.jpeg"
> }'
> ```
>
> ## Examples
>
> > The sun arcs across the sky from sunrise to sunset. Light transitions from cool dawn blue through warm golden midday to deep amber sunset. Shadows rotate and lengthen. Clouds drift and change shape. Smooth time-lapse feel.
>
> <video src="https://v3b.fal.media/files/b/0a946880/sfDxO-znDhQthS00Pa2Ns_d99979ba04614c3d963ebaa342627b3c.mp4" controls width="100%" />
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The text prompt describing the video you want to generate
>
> **aspect\_ratio** `AspectRatioEnum` (default: auto)
>
> The aspect ratio of the generated video. Default value: `"auto"`
>
> Possible values: `auto`, `16:9`, `9:16`
>
> **duration** `string` (default: 8s)
>
> The duration of the generated video. Default value: `"8s"`
>
> **negative\_prompt** `string`
>
> A negative prompt to guide the video generation.
>
> **resolution** `ResolutionEnum` (default: 720p)
>
> The resolution of the generated video. Default value: `"720p"`
>
> Possible values: `720p`, `1080p`
>
> **generate\_audio** `boolean` (default: true)
>
> Whether to generate audio for the video. Default value: `true`
>
> **seed** `integer`
>
> The seed for the random number generator.
>
> **auto\_fix** `boolean` (default: false)
>
> Whether to automatically attempt to fix prompts that fail content policy or other validation checks by rewriting them.
>
> **safety\_tolerance** `SafetyToleranceEnum` (default: 4)
>
> The safety tolerance level for content moderation. 1 is the most strict (blocks most content), 6 is the least strict. Default value: `"4"`
>
> Possible values: `1`, `2`, `3`, `4`, `5`, `6`
>
> **first\_frame\_url** `string` *required*
>
> URL of the first frame of the video
>
> **last\_frame\_url** `string` *required*
>
> URL of the last frame of the video
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
>   "prompt": "A smooth transition between the two frames with natural motion.",
>   "aspect_ratio": "auto",
>   "duration": "8s",
>   "resolution": "720p",
>   "generate_audio": true,
>   "auto_fix": false,
>   "safety_tolerance": "4",
>   "first_frame_url": "https://storage.googleapis.com/falserverless/example_inputs/veo31-flf2v-input-1.jpeg",
>   "last_frame_url": "https://storage.googleapis.com/falserverless/example_inputs/veo31-flf2v-input-2.jpeg"
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

* [Veo 3.1](https://fal.ai/docs/model-api-reference/video-generation-api/veo-3.1) — Video Generation
* [Veo 3.1 Fast](https://fal.ai/docs/model-api-reference/video-generation-api/veo-3.1-fast) — Video Generation
* [Veo3.1 Lite Image to Video](https://fal.ai/docs/model-api-reference/video-generation-api/veo3.1-lite-image-to-video) — Video Generation
* [Veo3.1 Lite FLF](https://fal.ai/docs/model-api-reference/video-generation-api/veo3.1-lite-flf) — Video Generation
* [Veo3.1 Lite Text to Video](https://fal.ai/docs/model-api-reference/video-generation-api/veo3.1-lite-text-to-video) — Video Generation

## Limitations

* `aspect_ratio` restricted to: `16:9`, `9:16`
* `duration` restricted to: `4s`, `6s`, `8s`
* `resolution` restricted to: `720p`, `1080p`
* `safety_tolerance` restricted to: `1`, `2`, `3`, `4`, `5`, `6`
* `aspect_ratio` restricted to: `auto`, `16:9`, `9:16`
