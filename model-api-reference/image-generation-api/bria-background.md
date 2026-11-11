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

# Bria Background API

> API reference for Bria Background. Bria RMBG 2.0 enables seamless removal of backgrounds from images, ideal for professional editing tasks. Trained exclusively on licensed data for safe and risk-free 

> [tab] Remove
>
> **Endpoint:** `POST https://fal.run/fal-ai/bria/background/remove`
> **Endpoint ID:** `fal-ai/bria/background/remove`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/bria/background/remove/playground)
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
>     "fal-ai/bria/background/remove",
>     arguments={
>         "image_url": "https://fal.media/files/panda/K5Rndvzmn1j-OI1VZXDVd.jpeg"
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
> const result = await fal.subscribe("fal-ai/bria/background/remove", {
>   input: {
>       image_url: "https://fal.media/files/panda/K5Rndvzmn1j-OI1VZXDVd.jpeg"
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
>   --url https://fal.run/fal-ai/bria/background/remove \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://fal.media/files/panda/K5Rndvzmn1j-OI1VZXDVd.jpeg"
> }'
> ```
>
> ## Examples
>
> ![Example output from Bria RMBG 2.0](https://v3.fal.media/files/lion/YBr6fXnaBoA_Hsy89qG6Q_44ab15cf50b0431fa4786c565c5b9032.png)
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> Input Image to erase from
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> ### Output Schema
>
> **image** `Image` *required*
>
> The generated image
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://fal.media/files/panda/K5Rndvzmn1j-OI1VZXDVd.jpeg",
>   "sync_mode": false
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "image": {
>     "content_type": "image/png",
>     "file_name": "070c731993e949d993c10ef6283d335d.png",
>     "file_size": 1076276,
>     "height": 1024,
>     "url": "https://v3.fal.media/files/tiger/GQEMNjRyxSoza7N8LPPqb_070c731993e949d993c10ef6283d335d.png",
>     "width": 1024
>   }
> }
> ```

> [tab] Replace
>
> **Endpoint:** `POST https://fal.run/fal-ai/bria/background/replace`
> **Endpoint ID:** `fal-ai/bria/background/replace`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/bria/background/replace/playground)
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
>     "fal-ai/bria/background/replace",
>     arguments={
>         "image_url": "https://storage.googleapis.com/falserverless/bria/bria_bg_replace_fg.jpg"
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
> const result = await fal.subscribe("fal-ai/bria/background/replace", {
>   input: {
>       image_url: "https://storage.googleapis.com/falserverless/bria/bria_bg_replace_fg.jpg"
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
>   --url https://fal.run/fal-ai/bria/background/replace \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://storage.googleapis.com/falserverless/bria/bria_bg_replace_fg.jpg"
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> Input Image to erase from
>
> **ref\_image\_url** `string`
>
> The URL of the reference image to be used for generating the new background. Use "" to leave empty. Either ref\_image\_url or bg\_prompt has to be provided but not both. If both ref\_image\_url and ref\_image\_file are provided, ref\_image\_url will be used. Accepted formats are jpeg, jpg, png, webp. Default value: `""`
>
> **prompt** `string`
>
> The prompt you would like to use to generate images.
>
> **negative\_prompt** `string`
>
> The negative prompt you would like to use to generate images. Default value: `""`
>
> **refine\_prompt** `boolean` (default: true)
>
> Whether to refine prompt Default value: `true`
>
> **seed** `integer`
>
> The same seed and the same prompt given to the same version of the model
> will output the same image every time.
>
> Range: `0` to `2147483647`
>
> **fast** `boolean` (default: true)
>
> Whether to use the fast model Default value: `true`
>
> **num\_images** `integer` (default: 1)
>
> Number of Images to generate. Default value: `1`
>
> Range: `1` to `4`
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> ### Output Schema
>
> **images** `list\<Image\>` *required*
>
> The generated images
>
> **seed** `integer` *required*
>
> Seed value used for generation.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://storage.googleapis.com/falserverless/bria/bria_bg_replace_fg.jpg",
>   "ref_image_url": "https://storage.googleapis.com/falserverless/bria/bria_bg_replace_bg.jpg",
>   "prompt": "Man leaning against a wall",
>   "negative_prompt": "",
>   "refine_prompt": true,
>   "fast": true,
>   "num_images": 1,
>   "sync_mode": false
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
>       "url": "https://storage.googleapis.com/falserverless/bria/bria_bg_replace_res.jpg"
>     }
>   ]
> }
> ```

Bria's RMBG 2.0 delivers production-grade background removal at \$0.018 per inference, trained exclusively on **licensed commercial data**. Trading breadth for legal certainty, this model solves the compliance headache that blocks most background removal tools from enterprise deployment. Built for teams that need defensible image segmentation without licensing risk.

**Use Cases:** E-commerce Product Photography | Marketing Asset Production | Professional Photo Editing

***

## Performance

At \$0.018 per generation, RMBG 2.0 is a **specialized utility model**, comparable to standard background removal pricing but with the critical differentiator of commercial licensing clarity.

| Metric | Result | Context |
| :- | :- | :- |
| **Training Data** | 100% licensed commercial imagery | Legal defensibility for enterprise deployment |
| **Cost per Image** | \$0.018 | 55 generations per \$1.00 on fal |
| **Input Format** | Single image URL | Standard image-to-image pipeline |
| **Output Format** | PNG with transparency | 1024x1024 preserved dimensions |
| **Related Endpoints** | [Background Replace](https://fal.ai/models/fal-ai/bria/background/replace), [Product Shot](https://fal.ai/models/fal-ai/bria/product-shot), [Expand Image](https://fal.ai/models/fal-ai/bria/expand) | Same-family variants for product photography workflows |

***

## Built for Commercial Deployment

RMBG 2.0 uses a **segmentation architecture** trained exclusively on licensed data, contrasting sharply with open models trained on scraped web imagery.

**What this means for you:**

* **Enterprise-safe training data:** Every training image carries commercial licensing, eliminating copyright exposure in regulated industries

* **Transparent background removal:** Clean PNG output with alpha channel preservation for immediate compositing workflows

* **Single-purpose optimization:** Focused solely on background segmentation rather than multi-task compromise

* **Straightforward integration:** Standard image URL input via [fal's API](https://fal.ai/models/fal-ai/bria/background/remove/api) with synchronous or asynchronous processing modes

***

## Technical Specifications

| Spec | Details |
| :- | :- |
| **Architecture** | Bria RMBG 2.0 |
| **Input Formats** | JPEG, PNG, WebP, GIF, AVIF via URL |
| **Output Formats** | PNG with alpha channel transparency |
| **Resolution** | Up to 1024x1024 (preserves input dimensions) |
| **License** | Commercial use with licensed training data |

[API Documentation](https://fal.ai/models/fal-ai/bria/background/remove/api) | [Quickstart Guide](https://docs.fal.ai/model-apis/quickstart) | [Enterprise Pricing](https://fal.ai/pricing)

***

## How It Stacks Up

\*\*Bria Background Replace ($0.023)** – RMBG 2.0 ($0.018) strips backgrounds cleanly, 22% more cost-effective. [Background Replace](https://fal.ai/models/fal-ai/bria/background/replace) adds generative fill capabilities for product staging workflows where you need both removal and context reconstruction.

\*\*Bria Product Shot ($0.023)** – RMBG 2.0 ($0.018) handles pure segmentation, while [Product Shot](https://fal.ai/models/fal-ai/bria/product-shot) combines removal with studio-quality scene generation for e-commerce listings. Product Shot trades simplicity for complete product photography automation at 28% higher cost.

\*\*Bria Expand Image ($0.023)** – RMBG 2.0 focuses on isolation; [Expand Image](https://fal.ai/models/fal-ai/bria/expand) handles outpainting for aspect ratio adjustments post-removal. Teams typically chain RMBG 2.0 into Expand for complete product asset workflows, with Expand adding generative borders at $0.023 per operation.

## Related

* [Bria Expand Image](https://fal.ai/docs/model-api-reference/image-generation-api/bria-expand-image) — Image Generation
* [Bria Eraser](bria-eraser.md) — Image Generation
* [Bria Product Shot](bria-product-shot.md) — Image Generation
* [Bria Background Replace](https://fal.ai/docs/model-api-reference/image-generation-api/bria-background-replace) — Image Generation
* [Video](https://fal.ai/docs/model-api-reference/video-generation-api/video) — Video Generation
* [Bria GenFill](bria-genfill.md) — Image Generation
* [Replace Background](https://fal.ai/docs/model-api-reference/image-generation-api/replace-background) — Image Generation
* [Bria Text-to-Image HD](https://fal.ai/docs/model-api-reference/image-generation-api/bria-text-to-image-hd) — Image Generation
* [Bria Text-to-Image Base](https://fal.ai/docs/model-api-reference/image-generation-api/bria-text-to-image-base) — Image Generation
* [Embed Product](https://fal.ai/docs/model-api-reference/image-generation-api/embed-product) — Image Generation
* [Bria Text-to-Image Fast](https://fal.ai/docs/model-api-reference/image-generation-api/bria-text-to-image-fast) — Image Generation
* [Bria RMBG 2.0](https://fal.ai/docs/model-api-reference/image-generation-api/bria-rmbg-2.0) — Image Generation

## Limitations

* `seed` range: 0 to 2147483647
* `num_images` range: 1 to 4
