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

# Nano Banana 2 API

> API reference for Nano Banana 2. Nano Banana 2 is Google's new state-of-the-art fast image generation and editing model

> [tab] Nano Banana 2
>
> **Endpoint:** `POST https://fal.run/fal-ai/nano-banana-2`
> **Endpoint ID:** `fal-ai/nano-banana-2`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/nano-banana-2/playground)
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
>     "fal-ai/nano-banana-2",
>     arguments={
>         "prompt": "An action shot of a black lab swimming in an inground suburban swimming pool. The camera is placed meticulously on the water line, dividing the image in half, revealing both the dogs head above water holding a tennis ball in it's mouth, and it's paws paddling underwater."
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
> const result = await fal.subscribe("fal-ai/nano-banana-2", {
>   input: {
>       prompt: "An action shot of a black lab swimming in an inground suburban swimming pool. The camera is placed meticulously on the water line, dividing the image in half, revealing both the dogs head above water holding a tennis ball in it's mouth, and it's paws paddling underwater."
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
>   --url https://fal.run/fal-ai/nano-banana-2 \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "An action shot of a black lab swimming in an inground suburban swimming pool. The camera is placed meticulously on the water line, dividing the image in half, revealing both the dogs head above water holding a tennis ball in it'\''s mouth, and it'\''s paws paddling underwater."
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The text prompt to generate an image from.
>
> **num\_images** `integer` (default: 1)
>
> The number of images to generate. Default value: `1`
>
> Range: `1` to `4`
>
> **seed** `integer`
>
> The seed for the random number generator.
>
> **aspect\_ratio** `Enum` (default: auto)
>
> The aspect ratio of the generated image. Supports extreme ratios: 4:1, 1:4, 8:1, 1:8. Use "auto" to let the model decide based on the prompt. Default value: `auto`
>
> Possible values: `auto`, `21:9`, `16:9`, `3:2`, `4:3`, `5:4`, `1:1`, `4:5`, `3:4`, `2:3`, `9:16`, `4:1`, `1:4`, `8:1`, `1:8`
>
> **output\_format** `OutputFormatEnum` (default: png)
>
> The format of the generated image. Default value: `"png"`
>
> Possible values: `jpeg`, `png`, `webp`
>
> **safety\_tolerance** `SafetyToleranceEnum` (default: 4)
>
> The safety tolerance level for content moderation. 1 is the most strict (blocks most content), 6 is the least strict. Default value: `"4"`
>
> Possible values: `1`, `2`, `3`, `4`, `5`, `6`
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **resolution** `ResolutionEnum` (default: 1K)
>
> The resolution of the image to generate. Default value: `"1K"`
>
> Possible values: `0.5K`, `1K`, `2K`, `4K`
>
> **limit\_generations** `boolean` (default: true)
>
> Experimental parameter to limit the number of generations from each round of prompting to 1. Set to `True` to to disregard any instructions in the prompt regarding the number of images to generate and ignore any intermediate images generated by the model. This may affect generation quality. Default value: `true`
>
> **enable\_web\_search** `boolean` (default: false)
>
> Enable web search for the image generation task. This will allow the model to use the latest information from the web to generate the image.
>
> **thinking\_level** `Enum`
>
> When set, enables model thinking with the given level ('minimal' or 'high') and includes thoughts in the generation. Omit to disable.
>
> Possible values: `minimal`, `high`
>
> ### Output Schema
>
> **images** `list\<ImageFile\>` *required*
>
> The generated images.
>
> **description** `string` *required*
>
> The description of the generated images.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "An action shot of a black lab swimming in an inground suburban swimming pool. The camera is placed meticulously on the water line, dividing the image in half, revealing both the dogs head above water holding a tennis ball in it's mouth, and it's paws paddling underwater.",
>   "num_images": 1,
>   "aspect_ratio": "auto",
>   "output_format": "png",
>   "safety_tolerance": "4",
>   "sync_mode": false,
>   "resolution": "1K",
>   "limit_generations": true,
>   "enable_web_search": false
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "images": [
>     {
>       "content_type": "image/png",
>       "file_name": "nano-banana-2-t2i-output.png",
>       "url": "https://storage.googleapis.com/falserverless/example_outputs/nano-banana-2-t2i-output.png"
>     }
>   ],
>   "description": ""
> }
> ```

> [tab] Edit
>
> **Endpoint:** `POST https://fal.run/fal-ai/nano-banana-2/edit`
> **Endpoint ID:** `fal-ai/nano-banana-2/edit`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/nano-banana-2/edit/playground)
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
>     "fal-ai/nano-banana-2/edit",
>     arguments={
>         "prompt": "make a photo of the man driving the car down the california coastline",
>         "image_urls": [
>             "https://storage.googleapis.com/falserverless/example_inputs/nano-banana-edit-input.png",
>             "https://storage.googleapis.com/falserverless/example_inputs/nano-banana-edit-input-2.png"
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
> const result = await fal.subscribe("fal-ai/nano-banana-2/edit", {
>   input: {
>       prompt: "make a photo of the man driving the car down the california coastline",
>       image_urls: [
>         "https://storage.googleapis.com/falserverless/example_inputs/nano-banana-edit-input.png",
>         "https://storage.googleapis.com/falserverless/example_inputs/nano-banana-edit-input-2.png"
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
>   --url https://fal.run/fal-ai/nano-banana-2/edit \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "make a photo of the man driving the car down the california coastline",
>   "image_urls": [
>     "https://storage.googleapis.com/falserverless/example_inputs/nano-banana-edit-input.png",
>     "https://storage.googleapis.com/falserverless/example_inputs/nano-banana-edit-input-2.png"
>   ]
> }'
> ```
>
> ## Examples
>
> > make a photo of the man driving the car down the california coastline
>
> ![Generated image: make a photo of the man driving the car down the california coastline](https://v3b.fal.media/files/b/0a95c08b/NQSC6eS_Ns1wyHUHslsB8_DIdSDU4X.png)
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The prompt for image editing.
>
> **num\_images** `integer` (default: 1)
>
> The number of images to generate. Default value: `1`
>
> Range: `1` to `4`
>
> **seed** `integer`
>
> The seed for the random number generator.
>
> **aspect\_ratio** `Enum` (default: auto)
>
> The aspect ratio of the generated image. Supports extreme ratios: 4:1, 1:4, 8:1, 1:8. Default value: `auto`
>
> Possible values: `auto`, `21:9`, `16:9`, `3:2`, `4:3`, `5:4`, `1:1`, `4:5`, `3:4`, `2:3`, `9:16`, `4:1`, `1:4`, `8:1`, `1:8`
>
> **output\_format** `OutputFormatEnum` (default: png)
>
> The format of the generated image. Default value: `"png"`
>
> Possible values: `jpeg`, `png`, `webp`
>
> **safety\_tolerance** `SafetyToleranceEnum` (default: 4)
>
> The safety tolerance level for content moderation. 1 is the most strict (blocks most content), 6 is the least strict. Default value: `"4"`
>
> Possible values: `1`, `2`, `3`, `4`, `5`, `6`
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **image\_urls** `list\<string\>` *required*
>
> The URLs of the images to use for image-to-image generation or image editing.
>
> **resolution** `ResolutionEnum` (default: 1K)
>
> The resolution of the image to generate. Default value: `"1K"`
>
> Possible values: `0.5K`, `1K`, `2K`, `4K`
>
> **limit\_generations** `boolean` (default: true)
>
> Experimental parameter to limit the number of generations from each round of prompting to 1. Set to `True` to to disregard any instructions in the prompt regarding the number of images to generate and ignore any intermediate images generated by the model. This may affect generation quality. Default value: `true`
>
> **enable\_web\_search** `boolean` (default: false)
>
> Enable web search for the image generation task. This will allow the model to use the latest information from the web to generate the image.
>
> **thinking\_level** `Enum`
>
> When set, enables model thinking with the given level ('minimal' or 'high') and includes thoughts in the generation. Omit to disable.
>
> Possible values: `minimal`, `high`
>
> ### Output Schema
>
> **images** `list\<ImageFile\>` *required*
>
> The edited images.
>
> **description** `string` *required*
>
> The description of the generated images.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "make a photo of the man driving the car down the california coastline",
>   "num_images": 1,
>   "aspect_ratio": "auto",
>   "output_format": "png",
>   "safety_tolerance": "4",
>   "sync_mode": false,
>   "image_urls": [
>     "https://storage.googleapis.com/falserverless/example_inputs/nano-banana-edit-input.png",
>     "https://storage.googleapis.com/falserverless/example_inputs/nano-banana-edit-input-2.png"
>   ],
>   "resolution": "1K",
>   "limit_generations": true,
>   "enable_web_search": false
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "images": [
>     {
>       "content_type": "image/png",
>       "file_name": "nano-banana-2-edit-output.png",
>       "url": "https://storage.googleapis.com/falserverless/example_outputs/nano-banana-2-edit-output.png"
>     }
>   ],
>   "description": ""
> }
> ```

## Nano Banana 2 \[text-to-image]

Google's Gemini 3.1 Flash Image architecture generates vibrant, high-fidelity visuals at speed, combining the reasoning capabilities of a multimodal foundation model with the efficiency of Flash-optimized inference. It understands creative intent holistically rather than matching keywords, producing images with accurate text rendering, character consistency, and coherent spatial composition in seconds.

**Built for:** Marketing campaigns and social media assets | Product photography and visualization | Designs requiring accurate in-image typography | Storyboarding with consistent characters across frames

### Reasoning-Guided, Flash-Fast

Built on Google's Gemini 3.1 Flash Image foundation, Nano Banana 2 reasons about composition, lighting, and spatial relationships before rendering. Unlike traditional diffusion models that treat prompts as weighted tokens, this architecture interprets creative direction as a multimodal language model would, capturing nuance and context that single-modality systems miss, then executes at Flash-tier speed.

**What this means for you:**

* **Vibrant output**: Rich color, punchy contrast, and visual coherence out of the box without post-processing
* **Accurate text rendering**: Character-by-character validated typography in multiple languages, directly in generated images
* **Character consistency**: Maintain identity for up to 5 people across generations for storyboarding and campaign work
* **Natural language control**: Describe mood, style, and context conversationally without mastering prompt engineering syntax
* **Web-grounded generation**: Optionally ground outputs in real-time web information for factually current visuals

### Technical Specifications

| Spec | Details |
| - | - |
| **Architecture** | Gemini 3.1 Flash Image (Nano Banana 2) |
| **Input** | Text prompts (natural language); up to 14 reference images for editing |
| **Output Formats** | PNG, JPEG, WebP |
| **Resolution** | 1K (default), 2K (1.5x rate), 4K (2x rate), 512x512 (0.75x rate) |
| **Aspect Ratios** | auto, 21:9, 16:9, 3:2, 4:3, 5:4, 1:1, 4:5, 3:4, 2:3, 9:16 |
| **Batch** | 1-4 images per request |
| **Character Consistency** | Up to 5 people across generations |
| **Watermarking** | SynthID digital watermarking on all outputs |
| **Web Search** | Optional grounding via `enable_web_search` or `enable_google_search` |
| **License** | Commercial use enabled through fal.ai |

[API Documentation](https://fal.ai/models/fal-ai/nano-banana-2/api)

### How It Stacks Up

**vs. Nano Banana Pro (Gemini 3 Pro Image)**: Nano Banana 2 prioritizes speed and vibrant output on the Flash architecture, generating in seconds where Pro optimizes for maximum reasoning depth at \$0.15/image. Choose Nano Banana 2 for fast iteration and production volume, Pro for maximum compositional complexity.

**vs. FLUX.2 \[dev]**: Nano Banana 2 delivers semantic-aware generation with native text rendering and character consistency through Gemini's multimodal reasoning. FLUX.2 \[dev] prioritizes resolution control and fine detail preservation for technical illustration workflows.

**vs. Original Nano Banana (Gemini 2.5 Flash Image)**: Nano Banana 2 adds reasoning-guided generation, dramatically improved text rendering, native multi-resolution output (1K/2K/4K), character consistency, multi-image compositing, and web search grounding. A generational leap in quality while maintaining Flash-tier speed.

## Related

* [Nano Banana 2](nano-banana-2.md) — Image Generation

## Limitations

* `num_images` range: 1 to 4
* `output_format` restricted to: `jpeg`, `png`, `webp`
* `safety_tolerance` restricted to: `1`, `2`, `3`, `4`, `5`, `6`
* `resolution` restricted to: `0.5K`, `1K`, `2K`, `4K`
* `thinking_level` restricted to: `minimal`, `high`
