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

# Gemini 3 Pro Image Preview API

> API reference for Gemini 3 Pro Image Preview. Gemini 3 Pro Image (a.k.a Nano Banana Pro) is Google's state-of-the-art high-fidelity image generation and editing model

> [tab] Gemini 3 Pro Image Preview
>
> **Endpoint:** `POST https://fal.run/fal-ai/gemini-3-pro-image-preview`
> **Endpoint ID:** `fal-ai/gemini-3-pro-image-preview`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/gemini-3-pro-image-preview/playground)
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
>     "fal-ai/gemini-3-pro-image-preview",
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
> const result = await fal.subscribe("fal-ai/gemini-3-pro-image-preview", {
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
>   --url https://fal.run/fal-ai/gemini-3-pro-image-preview \
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
> **aspect\_ratio** `Enum` (default: 1:1)
>
> The aspect ratio of the generated image. Default value: `1:1`
>
> Possible values: `auto`, `21:9`, `16:9`, `3:2`, `4:3`, `5:4`, `1:1`, `4:5`, `3:4`, `2:3`, `9:16`
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
> Possible values: `1K`, `2K`, `4K`
>
> **limit\_generations** `boolean` (default: false)
>
> Experimental parameter to limit the number of generations from each round of prompting to 1. Set to `True` to to disregard any instructions in the prompt regarding the number of images to generate.
>
> **enable\_web\_search** `boolean` (default: false)
>
> Enable web search for the image generation task. This will allow the model to use the latest information from the web to generate the image.
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
>   "aspect_ratio": "1:1",
>   "output_format": "png",
>   "safety_tolerance": "4",
>   "sync_mode": false,
>   "resolution": "1K",
>   "limit_generations": false,
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
>       "file_name": "nano-banana-pro-t2i-output.png",
>       "url": "https://storage.googleapis.com/falserverless/example_outputs/nano-banana-pro-t2i-output.png"
>     }
>   ],
>   "description": ""
> }
> ```

> [tab] Edit
>
> **Endpoint:** `POST https://fal.run/fal-ai/gemini-3-pro-image-preview/edit`
> **Endpoint ID:** `fal-ai/gemini-3-pro-image-preview/edit`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/gemini-3-pro-image-preview/edit/playground)
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
>     "fal-ai/gemini-3-pro-image-preview/edit",
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
> const result = await fal.subscribe("fal-ai/gemini-3-pro-image-preview/edit", {
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
>   --url https://fal.run/fal-ai/gemini-3-pro-image-preview/edit \
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
> The aspect ratio of the generated image. Default value: `auto`
>
> Possible values: `auto`, `21:9`, `16:9`, `3:2`, `4:3`, `5:4`, `1:1`, `4:5`, `3:4`, `2:3`, `9:16`
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
> Possible values: `1K`, `2K`, `4K`
>
> **limit\_generations** `boolean` (default: false)
>
> Experimental parameter to limit the number of generations from each round of prompting to 1. Set to `True` to to disregard any instructions in the prompt regarding the number of images to generate.
>
> **enable\_web\_search** `boolean` (default: false)
>
> Enable web search for the image generation task. This will allow the model to use the latest information from the web to generate the image.
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
>   "limit_generations": false,
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
>       "file_name": "nano-banana-pro-edit-output.png",
>       "url": "https://storage.googleapis.com/falserverless/example_outputs/nano-banana-pro-edit-output.png"
>     }
>   ],
>   "description": ""
> }
> ```

Google's Nano Banana Pro (2) Preview delivers state-of-the-art image generation at \$0.15 per image, trading cost efficiency for advanced reasoning capabilities and prompt understanding. Built on Gemini 3 Pro's multimodal architecture, it handles complex natural language instructions that would trip up traditional diffusion models.

**Use Cases:** Complex Scene Composition | Typography-Heavy Designs | Natural Language-Driven Editing

***

## Performance

Nano Banana Pro prioritizes semantic understanding over raw speed, making it ideal for projects where prompt accuracy matters more than generation volume.

| Metric | Result | Context |
| :- | :- | :- |
| **Resolution Range** | Up to 4K (4096px) | 4K outputs charged at 2x standard rate |
| **Cost per Image** | \$0.15 | 6-7 generations per \$1.00 on fal |
| **Batch Generation** | 1-4 images | Per request via `num_images` parameter |
| **Web Search Integration** | Optional | Enable via `enable_web_search` for current information |

***

## Advanced Prompt Understanding Through Gemini 3 Architecture

Gemini 3 Pro Image leverages Google's latest foundation model to interpret complex, conversational prompts rather than requiring carefully crafted keyword strings.

**What this means for you:**

* **Natural Language Processing:** Describe scenes conversationally ("meticulously placed camera on the water line, dividing the image in half") instead of keyword stuffing

* **Typography Excellence:** Tagged for realism and typography, making it particularly strong for text-heavy designs where letter accuracy matters

* **Flexible Output Control:** Generate up to 4 images per request with selectable aspect ratios (21:9 to 9:16) and formats (JPEG, PNG, WebP)

* **Resolution Scaling:** Choose 1K, 2K, or 4K output based on your quality-versus-cost tradeoff, with transparent 2x pricing for 4K

***

## Technical Specifications

| Spec | Details |
| :- | :- |
| **Architecture** | Gemini 3 Pro Image |
| **Input Formats** | Text prompts with optional web search grounding |
| **Output Formats** | JPEG, PNG, WebP |
| **Resolution Options** | 1K, 2K, 4K (4K at 2x cost) |
| **License** | Commercial use via partnership |

[API Documentation](https://fal.ai/models/fal-ai/gemini-3-pro-image-preview/api) | [Quickstart Guide](https://docs.fal.ai/model-apis/quickstart) | [Enterprise Pricing](https://fal.ai/pricing)

***

## How It Stacks Up

**[Gemini 2.5 Flash Image](https://fal.ai/models/fal-ai/gemini-25-flash-image)** – Nano Banana Pro trades speed and cost efficiency for advanced reasoning and complex instruction following at 4x the cost ($0.15 vs $0.039). Gemini 2.5 Flash Image remains ideal for high-volume workflows where rapid iteration matters more than nuanced prompt interpretation.

\*\*[AuraFlow](https://fal.ai/models/fal-ai/aura-flow) ($0.012)** – Nano Banana Pro prioritizes semantic understanding and typography accuracy at 12.5x the cost ($0.15 vs \$0.012). AuraFlow offers maximum cost efficiency for straightforward text-to-image generation where natural language complexity isn't critical.

## Related

* [Gemini 3 Pro Image Preview](gemini-3-pro-image-preview.md) — Image Generation

## Limitations

* `num_images` range: 1 to 4
* `output_format` restricted to: `jpeg`, `png`, `webp`
* `safety_tolerance` restricted to: `1`, `2`, `3`, `4`, `5`, `6`
* `resolution` restricted to: `1K`, `2K`, `4K`
