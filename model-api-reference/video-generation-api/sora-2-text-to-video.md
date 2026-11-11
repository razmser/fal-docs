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

# Sora 2 Text To Video API

> API reference for Sora 2 Text To Video. Text-to-video endpoint for Sora 2, OpenAI's state-of-the-art video model capable of creating richly detailed, dynamic clips with audio from natural language or 

> [tab] Text To Video
>
> **Endpoint:** `POST https://fal.run/fal-ai/sora-2/text-to-video`
> **Endpoint ID:** `fal-ai/sora-2/text-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/sora-2/text-to-video/playground)
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
>     "fal-ai/sora-2/text-to-video",
>     arguments={
>         "prompt": "A dramatic Hollywood breakup scene at dusk on a quiet suburban street. A man and a woman in their 30s face each other, speaking softly but emotionally, lips syncing to breakup dialogue. Cinematic lighting, warm sunset tones, shallow depth of field, gentle breeze moving autumn leaves, realistic natural sound, no background music"
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
> const result = await fal.subscribe("fal-ai/sora-2/text-to-video", {
>   input: {
>       prompt: "A dramatic Hollywood breakup scene at dusk on a quiet suburban street. A man and a woman in their 30s face each other, speaking softly but emotionally, lips syncing to breakup dialogue. Cinematic lighting, warm sunset tones, shallow depth of field, gentle breeze moving autumn leaves, realistic natural sound, no background music"
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
>   --url https://fal.run/fal-ai/sora-2/text-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A dramatic Hollywood breakup scene at dusk on a quiet suburban street. A man and a woman in their 30s face each other, speaking softly but emotionally, lips syncing to breakup dialogue. Cinematic lighting, warm sunset tones, shallow depth of field, gentle breeze moving autumn leaves, realistic natural sound, no background music"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The text prompt describing the video you want to generate
>
> **resolution** `string` (default: 720p)
>
> The resolution of the generated video Default value: `"720p"`
>
> **aspect\_ratio** `AspectRatioEnum` (default: 16:9)
>
> The aspect ratio of the generated video Default value: `"16:9"`
>
> Possible values: `9:16`, `16:9`
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
>   "prompt": "A dramatic Hollywood breakup scene at dusk on a quiet suburban street. A man and a woman in their 30s face each other, speaking softly but emotionally, lips syncing to breakup dialogue. Cinematic lighting, warm sunset tones, shallow depth of field, gentle breeze moving autumn leaves, realistic natural sound, no background music",
>   "resolution": "720p",
>   "aspect_ratio": "16:9",
>   "duration": 4,
>   "delete_video": true,
>   "model": "sora-2",
>   "detect_and_block_ip": false
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "content_type": "video/mp4",
>     "url": "https://storage.googleapis.com/falserverless/example_outputs/sora_t2v_output.mp4"
>   },
>   "video_id": "video_123"
> }
> ```

> [tab] Pro
>
> **Endpoint:** `POST https://fal.run/fal-ai/sora-2/text-to-video/pro`
> **Endpoint ID:** `fal-ai/sora-2/text-to-video/pro`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/sora-2/text-to-video/pro/playground)
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
>     "fal-ai/sora-2/text-to-video/pro",
>     arguments={
>         "prompt": "A dramatic Hollywood breakup scene at dusk on a quiet suburban street. A man and a woman in their 30s face each other, speaking softly but emotionally, lips syncing to breakup dialogue. Cinematic lighting, warm sunset tones, shallow depth of field, gentle breeze moving autumn leaves, realistic natural sound, no background music"
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
> const result = await fal.subscribe("fal-ai/sora-2/text-to-video/pro", {
>   input: {
>       prompt: "A dramatic Hollywood breakup scene at dusk on a quiet suburban street. A man and a woman in their 30s face each other, speaking softly but emotionally, lips syncing to breakup dialogue. Cinematic lighting, warm sunset tones, shallow depth of field, gentle breeze moving autumn leaves, realistic natural sound, no background music"
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
>   --url https://fal.run/fal-ai/sora-2/text-to-video/pro \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A dramatic Hollywood breakup scene at dusk on a quiet suburban street. A man and a woman in their 30s face each other, speaking softly but emotionally, lips syncing to breakup dialogue. Cinematic lighting, warm sunset tones, shallow depth of field, gentle breeze moving autumn leaves, realistic natural sound, no background music"
> }'
> ```
>
> ## Examples
>
> > A dramatic Hollywood breakup scene at dusk on a quiet suburban street. A man and a woman in their 30s face each other, speaking softly but emotionally, lips syncing to breakup dialogue. Cinematic lighting, warm sunset tones, shallow depth of field, gentle breeze moving autumn leaves, realistic natur...
>
> <video src="https://v3b.fal.media/files/b/0a91c0be/Yy-egGFV8E2ujRLxmrLKj_5vtsOrfM.mp4" controls width="100%" />
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The text prompt describing the video you want to generate
>
> **resolution** `ResolutionEnum` (default: 1080p)
>
> The resolution of the generated video Default value: `"1080p"`
>
> Possible values: `720p`, `1080p`, `true_1080p`
>
> **aspect\_ratio** `AspectRatioEnum` (default: 16:9)
>
> The aspect ratio of the generated video Default value: `"16:9"`
>
> Possible values: `9:16`, `16:9`
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
>   "prompt": "A dramatic Hollywood breakup scene at dusk on a quiet suburban street. A man and a woman in their 30s face each other, speaking softly but emotionally, lips syncing to breakup dialogue. Cinematic lighting, warm sunset tones, shallow depth of field, gentle breeze moving autumn leaves, realistic natural sound, no background music",
>   "resolution": "1080p",
>   "aspect_ratio": "16:9",
>   "duration": 4,
>   "delete_video": true,
>   "detect_and_block_ip": false
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "content_type": "video/mp4",
>     "url": "https://storage.googleapis.com/falserverless/example_outputs/sora-2-pro-t2v-output.mp4"
>   },
>   "video_id": "video_123"
> }
> ```

OpenAI's Sora 2 Pro generates up to 25-second videos with synchronized audio at \$0.50 per second for 1080p output. With **unprecedented length and audio integration**, Sora 2 excels where most competing models cap at 10 seconds without sound. Built for filmmakers, content creators, and developers who need production-ready clips with natural dialogue and environmental audio.

**Use Cases:** Cinematic Scene Generation | Marketing Video Production | AI-Assisted Filmmaking

***

## Performance

At $0.50/second for 1080p (or $0.30/second for 720p), Sora 2 Pro is a premium text-to-video solution, trading cost efficiency for industry-leading output length and **native audio synthesis**.

| Metric | Result | Context |
| :- | :- | :- |
| **Maximum Duration** | Up to 25 seconds | 2.5x longer than most competing models (10s standard) |
| **Resolution Options** | 720p, 1080p | Two quality tiers with tiered pricing |
| **Cost per Second** | $0.30 (720p), $0.50 (1080p) | Premium pricing for audio-enabled, extended-duration output |
| **Aspect Ratios** | 9:16, 16:9 | Vertical and horizontal formats for social and cinematic use |
| **Audio Synthesis** | Native audio generation | Synchronized dialogue, ambient sound, and environmental audio |
| **Related Endpoints** | [Sora 2 Video to Video](https://fal.ai/models/fal-ai/sora-2/video-to-video/remix), [Sora 2 Text to Video](https://fal.ai/models/fal-ai/sora-2/text-to-video) | Pro vs Standard tiers and remix capabilities |

***

## Audio-First Video Generation

Sora 2 Pro breaks from traditional silent video generation by synthesizing audio alongside visual content. Dialogue lip-syncing, environmental sounds, and ambient audio emerge from the same text prompt that describes the scene.

**What this means for you:**

* **Synchronized dialogue generation:** Characters speak naturally with accurate lip-sync to match emotional tone and scene context, no separate audio track required

* **Environmental audio integration:** Ambient sounds (wind, traffic, footsteps) generate contextually based on visual elements described in your prompt

* **Extended temporal coherence:** 25-second maximum duration maintains visual and audio consistency across longer narrative arcs than standard 4-10 second models

* **Flexible duration control:** Generate 4, 8, or 12-second clips for rapid iteration, or push to 25 seconds for complete scene development

***

## Technical Specifications

| Spec | Details |
| :- | :- |
| **Architecture** | Sora 2 Pro |
| **Input Formats** | Text prompts (natural language descriptions) |
| **Output Formats** | MP4 video with audio, optional thumbnail and spritesheet |
| **Resolution Options** | 720p ($0.30/s), 1080p ($0.50/s) |
| **Duration Range** | 4, 8, 12 seconds (standard), up to 25 seconds (extended) |
| **Aspect Ratios** | 9:16 (vertical), 16:9 (horizontal) |
| **License** | Commercial use via OpenAI API or fal credits |

[API Documentation](https://fal.ai/models/fal-ai/sora-2/text-to-video/pro/api) | [Quickstart Guide](https://docs.fal.ai/model-apis/quickstart) | [Enterprise Pricing](https://fal.ai/pricing)

***

## How It Stacks Up

**[Sora 2 Text to Video (Standard)](https://fal.ai/models/fal-ai/sora-2/text-to-video)** – Sora 2 Pro trades cost efficiency for extended duration and audio synthesis at premium pricing. Standard Sora 2 remains ideal for **rapid prototyping** and shorter clips where audio isn't required.

**[Hunyuan Video V1.5 Text to Video](https://fal.ai/models/fal-ai/hunyuan-video-v1.5/text-to-video)** – Sora 2 Pro prioritizes audio integration and extended temporal coherence (up to 25s) for narrative-driven content. Hunyuan Video V1.5 emphasizes cost-effective generation for standard-length clips without audio requirements.

**[LongCat Video Text to Video](http://fal.ai/models/fal-ai/longcat-video/text-to-video/720p)** – Sora 2 Pro delivers native audio synthesis and dialogue lip-syncing for production-ready scenes. LongCat Video focuses on visual-only generation with competitive pricing for silent video workflows.

## Related

* [Sora 2](https://fal.ai/docs/model-api-reference/video-generation-api/sora-2) — Video Generation

## Limitations

* `aspect_ratio` restricted to: `9:16`, `16:9`
* `duration` restricted to: `4`, `8`, `12`, `16`, `20`
* `model` restricted to: `sora-2`, `sora-2-2025-12-08`, `sora-2-2025-10-06`
* `resolution` restricted to: `720p`, `1080p`, `true_1080p`
