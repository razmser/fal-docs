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

# Flux Krea API

> API reference for Flux Krea. FLUX.1 Krea [dev] is a 12 billion parameter flow transformer that generates high-quality images from text with incredible aesthetics. It is suitable for personal and comme

> [tab] Krea
>
> **Endpoint:** `POST https://fal.run/fal-ai/flux/krea`
> **Endpoint ID:** `fal-ai/flux/krea`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/flux/krea/playground)
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
>     "fal-ai/flux/krea",
>     arguments={
>         "prompt": "A candid street photo of a woman with a pink bob and bold eyeliner on a graffiti-covered subway platform. She wears a bright yellow patent leather coat over a black-and-white checkered turtleneck and platform boots. Natural subway lighting creates an authentic urban scene with a relaxed, unposed feel."
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
> const result = await fal.subscribe("fal-ai/flux/krea", {
>   input: {
>       prompt: "A candid street photo of a woman with a pink bob and bold eyeliner on a graffiti-covered subway platform. She wears a bright yellow patent leather coat over a black-and-white checkered turtleneck and platform boots. Natural subway lighting creates an authentic urban scene with a relaxed, unposed feel."
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
>   --url https://fal.run/fal-ai/flux/krea \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A candid street photo of a woman with a pink bob and bold eyeliner on a graffiti-covered subway platform. She wears a bright yellow patent leather coat over a black-and-white checkered turtleneck and platform boots. Natural subway lighting creates an authentic urban scene with a relaxed, unposed feel."
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The prompt to generate an image from.
>
> **image\_size** `ImageSize | Enum` (default: landscape\_4\_3)
>
> The size of the generated image. Default value: `landscape_4_3`
>
> Possible values: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`
>
> **num\_inference\_steps** `integer` (default: 28)
>
> The number of inference steps to perform. Default value: `28`
>
> Range: `1` to `50`
>
> **seed** `integer`
>
> The same seed and the same prompt given to the same version of the model
> will output the same image every time.
>
> **guidance\_scale** `float` (default: 4.5)
>
> The CFG (Classifier Free Guidance) scale is a measure of how close you want
> the model to stick to your prompt when looking for a related image to show you. Default value: `4.5`
>
> Range: `1` to `20`
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **num\_images** `integer` (default: 1)
>
> The number of images to generate. Default value: `1`
>
> Range: `1` to `4`
>
> **enable\_safety\_checker** `boolean` (default: true)
>
> If set to true, the safety checker will be enabled. Default value: `true`
>
> **output\_format** `OutputFormatEnum` (default: jpeg)
>
> The format of the generated image. Default value: `"jpeg"`
>
> Possible values: `jpeg`, `png`
>
> **acceleration** `AccelerationEnum` (default: none)
>
> The speed of the generation. The higher the speed, the faster the generation. Default value: `"none"`
>
> Possible values: `none`, `regular`, `high`
>
> ### Output Schema
>
> **images** `list\<Image\>` *required*
>
> The generated images.
>
> **timings** `Timings` *required*
>
> **seed** `integer` *required*
>
> Seed of the generated Image. It will be the same value of the one passed in the
> input or the randomly generated that was used in case none was passed.
>
> **has\_nsfw\_concepts** `list\<boolean\>` *required*
>
> Whether the generated images contain NSFW concepts.
>
> **prompt** `string` *required*
>
> The prompt used for generating the image.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "A candid street photo of a woman with a pink bob and bold eyeliner on a graffiti-covered subway platform. She wears a bright yellow patent leather coat over a black-and-white checkered turtleneck and platform boots. Natural subway lighting creates an authentic urban scene with a relaxed, unposed feel.",
>   "image_size": "landscape_4_3",
>   "num_inference_steps": 28,
>   "guidance_scale": 4.5,
>   "sync_mode": false,
>   "num_images": 1,
>   "enable_safety_checker": true,
>   "output_format": "jpeg",
>   "acceleration": "none"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "images": [
>     {
>       "content_type": "image/jpeg",
>       "height": 768,
>       "url": "https://storage.googleapis.com/falserverless/example_outputs/flux_krea_t2i_output_1.jpg",
>       "width": 1024
>     }
>   ],
>   "prompt": ""
> }
> ```

> [tab] Image To Image
>
> **Endpoint:** `POST https://fal.run/fal-ai/flux/krea/image-to-image`
> **Endpoint ID:** `fal-ai/flux/krea/image-to-image`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/flux/krea/image-to-image/playground)
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
>     "fal-ai/flux/krea/image-to-image",
>     arguments={
>         "image_url": "https://fal.media/files/koala/Chls9L2ZnvuipUTEwlnJC.png",
>         "prompt": "A cat dressed as a wizard with a background of a mystic forest."
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
> const result = await fal.subscribe("fal-ai/flux/krea/image-to-image", {
>   input: {
>       image_url: "https://fal.media/files/koala/Chls9L2ZnvuipUTEwlnJC.png",
>       prompt: "A cat dressed as a wizard with a background of a mystic forest."
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
>   --url https://fal.run/fal-ai/flux/krea/image-to-image \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://fal.media/files/koala/Chls9L2ZnvuipUTEwlnJC.png",
>   "prompt": "A cat dressed as a wizard with a background of a mystic forest."
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> The URL of the image to generate an image from.
>
> **strength** `float` (default: 0.95)
>
> The strength of the initial image. Higher strength values are better for this model. Default value: `0.95`
>
> Range: `0.01` to `1`
>
> **num\_inference\_steps** `integer` (default: 40)
>
> The number of inference steps to perform. Default value: `40`
>
> Range: `10` to `50`
>
> **prompt** `string` *required*
>
> The prompt to generate an image from.
>
> **seed** `integer`
>
> The same seed and the same prompt given to the same version of the model
> will output the same image every time.
>
> **guidance\_scale** `float` (default: 4.5)
>
> The CFG (Classifier Free Guidance) scale is a measure of how close you want
> the model to stick to your prompt when looking for a related image to show you. Default value: `4.5`
>
> Range: `1` to `20`
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **num\_images** `integer` (default: 1)
>
> The number of images to generate. Default value: `1`
>
> Range: `1` to `4`
>
> **enable\_safety\_checker** `boolean` (default: true)
>
> If set to true, the safety checker will be enabled. Default value: `true`
>
> **output\_format** `OutputFormatEnum` (default: jpeg)
>
> The format of the generated image. Default value: `"jpeg"`
>
> Possible values: `jpeg`, `png`
>
> **acceleration** `AccelerationEnum` (default: none)
>
> The speed of the generation. The higher the speed, the faster the generation. Default value: `"none"`
>
> Possible values: `none`, `regular`, `high`
>
> ### Output Schema
>
> **images** `list\<Image\>` *required*
>
> The generated image files info.
>
> **timings** `Timings` *required*
>
> **seed** `integer` *required*
>
> Seed of the generated Image. It will be the same value of the one passed in the
> input or the randomly generated that was used in case none was passed.
>
> **has\_nsfw\_concepts** `list\<boolean\>` *required*
>
> Whether the generated images contain NSFW concepts.
>
> **prompt** `string` *required*
>
> The prompt used for generating the image.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://fal.media/files/koala/Chls9L2ZnvuipUTEwlnJC.png",
>   "strength": 0.95,
>   "num_inference_steps": 40,
>   "prompt": "A cat dressed as a wizard with a background of a mystic forest.",
>   "guidance_scale": 4.5,
>   "sync_mode": false,
>   "num_images": 1,
>   "enable_safety_checker": true,
>   "output_format": "jpeg",
>   "acceleration": "none"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "images": [
>     {
>       "url": "",
>       "content_type": "image/jpeg"
>     }
>   ],
>   "prompt": ""
> }
> ```

> [tab] Redux
>
> **Endpoint:** `POST https://fal.run/fal-ai/flux/krea/redux`
> **Endpoint ID:** `fal-ai/flux/krea/redux`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/flux/krea/redux/playground)
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
>     "fal-ai/flux/krea/redux",
>     arguments={
>         "image_url": "https://storage.googleapis.com/falserverless/example_inputs/flux_krea_redux_output_1.jpg"
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
> const result = await fal.subscribe("fal-ai/flux/krea/redux", {
>   input: {
>       image_url: "https://storage.googleapis.com/falserverless/example_inputs/flux_krea_redux_output_1.jpg"
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
>   --url https://fal.run/fal-ai/flux/krea/redux \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/flux_krea_redux_output_1.jpg"
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> The URL of the image to generate an image from.
>
> **image\_size** `ImageSize | Enum` (default: landscape\_4\_3)
>
> The size of the generated image. Default value: `landscape_4_3`
>
> Possible values: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`
>
> **num\_inference\_steps** `integer` (default: 28)
>
> The number of inference steps to perform. Default value: `28`
>
> Range: `1` to `50`
>
> **seed** `integer`
>
> The same seed and the same prompt given to the same version of the model
> will output the same image every time.
>
> **guidance\_scale** `float` (default: 4.5)
>
> The CFG (Classifier Free Guidance) scale is a measure of how close you want
> the model to stick to your prompt when looking for a related image to show you. Default value: `4.5`
>
> Range: `1` to `20`
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **num\_images** `integer` (default: 1)
>
> The number of images to generate. Default value: `1`
>
> Range: `1` to `4`
>
> **enable\_safety\_checker** `boolean` (default: true)
>
> If set to true, the safety checker will be enabled. Default value: `true`
>
> **output\_format** `OutputFormatEnum` (default: jpeg)
>
> The format of the generated image. Default value: `"jpeg"`
>
> Possible values: `jpeg`, `png`
>
> **acceleration** `AccelerationEnum` (default: none)
>
> The speed of the generation. The higher the speed, the faster the generation. Default value: `"none"`
>
> Possible values: `none`, `regular`, `high`
>
> ### Output Schema
>
> **images** `list\<Image\>` *required*
>
> The generated images.
>
> **timings** `Timings` *required*
>
> **seed** `integer` *required*
>
> Seed of the generated Image. It will be the same value of the one passed in the
> input or the randomly generated that was used in case none was passed.
>
> **has\_nsfw\_concepts** `list\<boolean\>` *required*
>
> Whether the generated images contain NSFW concepts.
>
> **prompt** `string` *required*
>
> The prompt used for generating the image.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://storage.googleapis.com/falserverless/example_inputs/flux_krea_redux_output_1.jpg",
>   "image_size": "landscape_4_3",
>   "num_inference_steps": 28,
>   "guidance_scale": 4.5,
>   "sync_mode": false,
>   "num_images": 1,
>   "enable_safety_checker": true,
>   "output_format": "jpeg",
>   "acceleration": "none"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "images": [
>     {
>       "content_type": "image/jpeg",
>       "height": 768,
>       "url": "https://storage.googleapis.com/falserverless/example_outputs/flux_krea_redux_output_1.jpg",
>       "width": 1024
>     }
>   ],
>   "prompt": ""
> }
> ```

Black Forest Labs' FLUX.1 Krea \[dev] delivers high-quality image generation from a **12 billion parameter flow transformer** at \$0.025 per megapixel. Trading raw parameter count for aesthetic refinement through collaboration with Krea AI, this model prioritizes visual quality and commercial viability over pure speed. Built for creators and enterprises needing production-ready images without the licensing restrictions of research-only alternatives.

**Use Cases:** Commercial Product Photography | Marketing Asset Generation | Creative Concept Visualization

***

## Performance

FLUX.1 Krea \[dev] is a commercially-licensed alternative in the FLUX family, offering flexible resolution scaling with per-megapixel pricing that becomes cost-competitive at lower resolutions, delivering 40 generations per \$1.00 at standard sizes.

| Metric | Result | Context |
| :- | :- | :- |
| **Parameter Count** | 12 billion | Flow transformer architecture optimized for aesthetic quality |
| **Inference Steps** | 28 (default) | Configurable 1-50 steps for speed/quality tradeoff |
| **Cost per Megapixel** | \$0.025 | 40 generations per \$1.00 at 1MP; images billed by rounding up to nearest megapixel |
| **Max Resolution** | Up to 4 megapixels | Flexible aspect ratios via landscape/portrait/square presets |
| **Batch Generation** | 1-4 images | Single API call generates multiple variations |
| **Related Endpoints** | [FLUX.1 \[dev\]](https://fal.ai/models/fal-ai/flux/dev), [FLUX.1 SRPO \[dev\]](https://fal.ai/models/fal-ai/flux/srpo) | Standard dev vs aesthetic-tuned vs style-refined variants |

***

## Commercial-Ready Generation with Aesthetic Focus

FLUX.1 Krea \[dev] implements a 12 billion parameter flow transformer architecture tuned specifically for aesthetic quality through Black Forest Labs' collaboration with Krea AI. Unlike research-licensed models that restrict commercial use, this variant ships with full commercial permissions while maintaining the prompt interpretation capabilities of the base FLUX.1 architecture.

**What this means for you:**

* **Flexible Resolution Scaling:** Generate from sub-megapixel thumbnails to 4MP hero images with per-megapixel billing, paying only for the resolution you need rather than flat per-image costs

* **Configurable Quality Control:** Adjust inference steps (1-50) and guidance scale (1-20) to balance generation speed against prompt adherence for different production workflows

* **Built-in Safety Filtering:** Optional safety checker validates outputs against NSFW concepts before delivery, reducing moderation overhead for customer-facing applications

* **Batch Efficiency:** Generate up to 4 variations per API call with consistent seed control for A/B testing creative concepts without multiple requests

***

## Technical Specifications

| Spec | Details |
| :- | :- |
| **Architecture** | FLUX.1 Krea \[dev] |
| **Input Formats** | Text prompts with optional seed control |
| **Output Formats** | JPEG, PNG |
| **Max Resolution** | 4 megapixels with preset aspect ratios (landscape\_4\_3, portrait\_16\_9, square\_1\_1) |
| **License** | Commercial use permitted |

[API Documentation](https://fal.ai/models/fal-ai/flux/krea/api) | [Quickstart Guide](https://docs.fal.ai/model-apis/quickstart) | [Enterprise Pricing](https://fal.ai/pricing)

***

## How It Stacks Up

**[FLUX.1 \[dev\]](https://fal.ai/models/fal-ai/flux/dev) (\$0.025/MP)** – FLUX.1 Krea \[dev] trades the base model's research-only license for commercial permissions while adding aesthetic tuning from Krea AI's dataset at identical pricing. FLUX.1 \[dev] remains ideal for research applications where licensing restrictions aren't a concern.

**[FLUX.1 SRPO \[dev\]](https://fal.ai/models/fal-ai/flux/srpo) (\$0.025/MP)** – FLUX.1 Krea \[dev] prioritizes aesthetic quality through Krea's training data, while SRPO emphasizes style refinement through reinforcement learning. Both share commercial licensing and per-megapixel pricing, with SRPO offering stronger stylistic control for brand-consistent generation.

\*\*[Flux 2 Pro](https://fal.ai/models/fal-ai/flux-2-pro) ($0.055/MP)** – FLUX.1 Krea [dev] delivers commercially-licensed generation at 2.2x lower cost ($0.025 vs \$0.055 per megapixel). Flux 2 Pro provides next-generation prompt understanding and detail preservation for applications where maximum quality justifies the premium pricing.

\*\*[Flux 2 Flex](https://fal.ai/models/fal-ai/flux-2-flex) ($0.035/MP)** – FLUX.1 Krea [dev] offers 1.4x cost savings ($0.025 vs \$0.035 per megapixel) while maintaining commercial licensing. Flux 2 Flex balances the quality improvements of Flux 2 architecture with more accessible pricing for high-volume generation workflows.

## Related

* [FLUX.1 Krea \[dev\]](https://fal.ai/docs/model-api-reference/image-generation-api/flux.1-krea) — Image Generation
* [FLUX.1 Krea \[dev\] Redux](https://fal.ai/docs/model-api-reference/image-generation-api/flux.1-krea-redux) — Image Generation

## Limitations

* `image_size` restricted to: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`
* `num_inference_steps` range: 1 to 50
* `guidance_scale` range: 1 to 20
* `num_images` range: 1 to 4
* `output_format` restricted to: `jpeg`, `png`
* `acceleration` restricted to: `none`, `regular`, `high`
* Content moderation via safety checker
* `strength` range: 0.01 to 1
* `num_inference_steps` range: 10 to 50
