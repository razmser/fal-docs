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

# Kling Video V2.6 Pro API

> API reference for Kling Video V2.6 Pro. Kling 2.6 Pro: Top-tier image-to-video with cinematic visuals, fluid motion, and native audio generation.

> [tab] Image To Video
>
> **Endpoint:** `POST https://fal.run/fal-ai/kling-video/v2.6/pro/image-to-video`
> **Endpoint ID:** `fal-ai/kling-video/v2.6/pro/image-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/v2.6/pro/image-to-video/playground)
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
>     "fal-ai/kling-video/v2.6/pro/image-to-video",
>     arguments={
>         "prompt": "A king walks slowly and says \"My people, here I am! I am here to save you all\"",
>         "start_image_url": "https://v3b.fal.media/files/b/0a84ab29/BSJXz9Ht-jgRgMf4IGxLU_upscaled.png"
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
> const result = await fal.subscribe("fal-ai/kling-video/v2.6/pro/image-to-video", {
>   input: {
>       prompt: "A king walks slowly and says \"My people, here I am! I am here to save you all\"",
>       start_image_url: "https://v3b.fal.media/files/b/0a84ab29/BSJXz9Ht-jgRgMf4IGxLU_upscaled.png"
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
>   --url https://fal.run/fal-ai/kling-video/v2.6/pro/image-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A king walks slowly and says \"My people, here I am! I am here to save you all\"",
>   "start_image_url": "https://v3b.fal.media/files/b/0a84ab29/BSJXz9Ht-jgRgMf4IGxLU_upscaled.png"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> **start\_image\_url** `string` *required*
>
> URL of the image to be used for the video
>
> **duration** `DurationEnum` (default: 5)
>
> The duration of the generated video in seconds Default value: `"5"`
>
> Possible values: `5`, `10`
>
> **negative\_prompt** `string` (default: blur, distort, and low quality)
>
> Default value: `"blur, distort, and low quality"`
>
> **generate\_audio** `boolean` (default: true)
>
> Whether to generate native audio for the video. Supports Chinese and English voice output. Other languages are automatically translated to English. For English speech, use lowercase letters; for acronyms or proper nouns, use uppercase. Default value: `true`
>
> **voice\_ids** `list\<string\>`
>
> Optional Voice IDs for video generation. Reference voices in your prompt with \<\<\<voice\_1>>> and \<\<\<voice\_2>>> (maximum 2 voices per task). Get voice IDs from the kling video create-voice endpoint: [https://fal.ai/models/fal-ai/kling-video/create-voice](https://fal.ai/models/fal-ai/kling-video/create-voice)
>
> **end\_image\_url** `string`
>
> URL of the image to be used for the end of the video
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
>   "prompt": "A king walks slowly and says \"My people, here I am! I am here to save you all\"",
>   "start_image_url": "https://v3b.fal.media/files/b/0a84ab29/BSJXz9Ht-jgRgMf4IGxLU_upscaled.png",
>   "duration": "5",
>   "negative_prompt": "blur, distort, and low quality",
>   "generate_audio": true
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
>     "file_size": 11814817,
>     "url": "https://v3b.fal.media/files/b/0a84ab51/Qr1twf8UgtD5rZHpNXC2P_output.mp4"
>   }
> }
> ```

> [tab] Text To Video
>
> **Endpoint:** `POST https://fal.run/fal-ai/kling-video/v2.6/pro/text-to-video`
> **Endpoint ID:** `fal-ai/kling-video/v2.6/pro/text-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/v2.6/pro/text-to-video/playground)
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
>     "fal-ai/kling-video/v2.6/pro/text-to-video",
>     arguments={
>         "prompt": "Old friends reuniting at a train station after 20 years, one exclaims 'Is that really you?!' other tearfully replies 'I promised I'd come back, didn't I?', train whistle, steam hissing, emotional orchestral swell, crowd murmur"
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
> const result = await fal.subscribe("fal-ai/kling-video/v2.6/pro/text-to-video", {
>   input: {
>       prompt: "Old friends reuniting at a train station after 20 years, one exclaims 'Is that really you?!' other tearfully replies 'I promised I'd come back, didn't I?', train whistle, steam hissing, emotional orchestral swell, crowd murmur"
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
>   --url https://fal.run/fal-ai/kling-video/v2.6/pro/text-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Old friends reuniting at a train station after 20 years, one exclaims '\''Is that really you?!'\'' other tearfully replies '\''I promised I'\''d come back, didn'\''t I?'\'', train whistle, steam hissing, emotional orchestral swell, crowd murmur"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> **duration** `DurationEnum` (default: 5)
>
> The duration of the generated video in seconds Default value: `"5"`
>
> Possible values: `5`, `10`
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
> **generate\_audio** `boolean` (default: true)
>
> Whether to generate native audio for the video. Supports Chinese and English voice output. Other languages are automatically translated to English. For English speech, use lowercase letters; for acronyms or proper nouns, use uppercase. Default value: `true`
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
>   "prompt": "Old friends reuniting at a train station after 20 years, one exclaims 'Is that really you?!' other tearfully replies 'I promised I'd come back, didn't I?', train whistle, steam hissing, emotional orchestral swell, crowd murmur",
>   "duration": "5",
>   "aspect_ratio": "16:9",
>   "negative_prompt": "blur, distort, and low quality",
>   "cfg_scale": 0.5,
>   "generate_audio": true
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
>     "file_size": 8195664,
>     "url": "https://v3b.fal.media/files/b/0a84ab71/8hPbLs7n59WhWY-BN69yX_output.mp4"
>   }
> }
> ```

> [tab] Motion Control
>
> **Endpoint:** `POST https://fal.run/fal-ai/kling-video/v2.6/pro/motion-control`
> **Endpoint ID:** `fal-ai/kling-video/v2.6/pro/motion-control`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/v2.6/pro/motion-control/playground)
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
>     "fal-ai/kling-video/v2.6/pro/motion-control",
>     arguments={
>         "image_url": "https://v3b.fal.media/files/b/0a875302/8NaxQrQxDNHppHtqcchMm.png",
>         "video_url": "https://v3b.fal.media/files/b/0a8752bc/2xrNS217ngQ3wzXqA7LXr_output.mp4",
>         "character_orientation": "video"
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
> const result = await fal.subscribe("fal-ai/kling-video/v2.6/pro/motion-control", {
>   input: {
>       image_url: "https://v3b.fal.media/files/b/0a875302/8NaxQrQxDNHppHtqcchMm.png",
>       video_url: "https://v3b.fal.media/files/b/0a8752bc/2xrNS217ngQ3wzXqA7LXr_output.mp4",
>       character_orientation: "video"
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
>   --url https://fal.run/fal-ai/kling-video/v2.6/pro/motion-control \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://v3b.fal.media/files/b/0a875302/8NaxQrQxDNHppHtqcchMm.png",
>   "video_url": "https://v3b.fal.media/files/b/0a8752bc/2xrNS217ngQ3wzXqA7LXr_output.mp4",
>   "character_orientation": "video"
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
>   "prompt": "An african american woman dancing",
>   "image_url": "https://v3b.fal.media/files/b/0a875302/8NaxQrQxDNHppHtqcchMm.png",
>   "video_url": "https://v3b.fal.media/files/b/0a8752bc/2xrNS217ngQ3wzXqA7LXr_output.mp4",
>   "keep_original_sound": true,
>   "character_orientation": "video"
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
>     "file_size": 35299865,
>     "url": "https://v3b.fal.media/files/b/0a875336/8p3rFiXtx3fE2TLoh59KP_output.mp4"
>   }
> }
> ```

Kuaishou's Kling 2.6 Pro delivers cinematic image-to-video generation with native audio synthesis at $0.07 per second (audio off) or $0.14 per second (audio on). Trading compute intensity for production-grade motion quality and integrated speech generation, this positions as a top-tier solution for content creators requiring broadcast-ready output. Built for teams that need audio-visual coherence without post-production stitching.

**Built for:** Social Media Content Creation | Marketing Video Production | Cinematic Prototyping

***

## Native Audio Generation Meets Fluid Motion

Kling 2.6 Pro's architecture integrates speech synthesis directly into the video generation pipeline, supporting Chinese and English voice output with automatic translation for other languages. This contrasts with standard image-to-video models that require separate audio workflows and manual synchronization.

**What this means for you:**

* **Synchronized audio-visual output:** Generate videos with native speech that matches lip movements and scene timing, eliminating post-production audio alignment work
* **Flexible duration control:** Choose between 5-second or 10-second outputs based on content requirements and budget constraints
* **Single-image animation:** Transform static images into fluid video sequences with cinematic motion quality and scene continuity
* **Prompt-driven speech:** Embed dialogue directly in prompts (e.g., "A king walks slowly and says 'My people, here I am!'") for automatic voice generation with proper capitalization handling for English pronunciation

***

## Performance That Scales

Kling 2.6 Pro prioritizes output quality and audio integration over generation speed, positioning as a production-focused solution rather than rapid iteration tool.

| Metric | Result | Context |
| :- | :- | :- |
| **Duration Options** | 5s or 10s | Configurable via API parameter |
| **Cost per Second** | $0.07 (no audio) / $0.14 (with audio) | 5s video with audio = \$0.70 total |
| **Audio Languages** | Chinese, English (native) + auto-translation | Uppercase for acronyms/proper nouns in English |
| **Input Format** | Single image URL | Accepts jpg, jpeg, png, webp, gif, avif |

***

## Technical Specifications

| Spec | Details |
| :- | :- |
| **Architecture** | Kling 2.6 Pro |
| **Input Formats** | Image URL (jpg, jpeg, png, webp, gif, avif) |
| **Output Formats** | MP4 video with optional audio track |
| **Duration Control** | 5 or 10 seconds (configurable) |
| **License** | Commercial use permitted (Partner) |

[API Documentation](https://fal.ai/models/fal-ai/kling-video/v2.6/pro/image-to-video/api)

***

## How It Stacks Up

**Kling Video Image to Video (v2.5-turbo)** - Kling 2.6 Pro trades generation speed for native audio synthesis and enhanced motion quality, making it ideal for production workflows requiring integrated speech output. The [v2.5-turbo variant](https://fal.ai/models/fal-ai/kling-video/v2.5-turbo/pro/image-to-video) prioritizes faster iteration cycles for teams testing concepts without audio requirements.

**Kling 1.6 Image to Video** - Kling 2.6 Pro offers native audio generation and refined motion fidelity compared to the [1.6 baseline](https://fal.ai/models/fal-ai/kling-video/v1.6/pro/image-to-video), positioning as the premium tier for broadcast-quality output. Version 1.6 remains viable for projects where audio integration isn't critical.

**Kling 2.0 Master Image to Video** - Kling 2.6 Pro extends the 2.0 architecture with improved speech synthesis capabilities and motion coherence. The [2.0 Master variant](https://fal.ai/models/fal-ai/kling-video/v2/master/image-to-video) serves workflows requiring the previous generation's specific characteristics or [pricing structure](https://fal.ai/pricing).

**Kling 2.1 (standard) Image to Video** - Kling 2.6 Pro delivers enhanced audio quality and cinematic motion compared to the [2.1 standard tier](https://fal.ai/models/fal-ai/kling-video/v2.1/standard/image-to-video). The 2.1 standard remains cost-effective for projects where Pro-level audio fidelity isn't essential.

## Related

* [Kling Video v2.6 Motion Control \[Standard\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-video-v2.6-motion-control) — Video Generation
* [Kling Video v2.6 Text to Video](https://fal.ai/docs/model-api-reference/video-generation-api/kling-video-v2.6-text-to-video) — Video Generation
* [Kling Video v2.6 Image to Video](https://fal.ai/docs/model-api-reference/video-generation-api/kling-video-v2.6-image-to-video) — Video Generation

## Limitations

* `duration` restricted to: `5`, `10`
* `aspect_ratio` restricted to: `16:9`, `9:16`, `1:1`
* `cfg_scale` range: 0 to 1
* `character_orientation` restricted to: `image`, `video`
