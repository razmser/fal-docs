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

# Bria Genfill API

> API reference for Bria Genfill. Bria GenFill enables high-quality object addition or visual transformation. Trained exclusively on licensed data for safe and risk-free commercial use. Access the model

**Endpoint:** `POST https://fal.run/fal-ai/bria/genfill`
**Endpoint ID:** `fal-ai/bria/genfill`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/bria/genfill/playground)
>
> Run this model interactively with your own prompts.

## Quick Start

```python title="Python" theme={null}
import fal_client

def on_queue_update(update):
    if isinstance(update, fal_client.InProgress):
        for log in update.logs:
           print(log["message"])

result = fal_client.subscribe(
    "fal-ai/bria/genfill",
    arguments={
        "image_url": "https://storage.googleapis.com/falserverless/bria/bria_genfill_img.png",
        "mask_url": "https://storage.googleapis.com/falserverless/bria/bria_genfill_mask.png",
        "prompt": "A red delicious cherry"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/bria/genfill", {
  input: {
      image_url: "https://storage.googleapis.com/falserverless/bria/bria_genfill_img.png",
      mask_url: "https://storage.googleapis.com/falserverless/bria/bria_genfill_mask.png",
      prompt: "A red delicious cherry"
    },
  logs: true,
  onQueueUpdate: (update) => {
    if (update.status === "IN_PROGRESS") {
      update.logs.map((log) => log.message).forEach(console.log);
    }
  },
});
console.log(result.data);
console.log(result.requestId);
```

```bash title="cURL" theme={null}
curl --request POST \
  --url https://fal.run/fal-ai/bria/genfill \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "image_url": "https://storage.googleapis.com/falserverless/bria/bria_genfill_img.png",
  "mask_url": "https://storage.googleapis.com/falserverless/bria/bria_genfill_mask.png",
  "prompt": "A red delicious cherry"
}'
```

## Related

* [Bria RMBG 2.0](https://fal.ai/docs/model-api-reference/image-generation-api/bria-rmbg-2.0) — Image Generation
* [Bria Expand Image](https://fal.ai/docs/model-api-reference/image-generation-api/bria-expand-image) — Image Generation
* [Bria Eraser](bria-eraser.md) — Image Generation
* [Bria Product Shot](bria-product-shot.md) — Image Generation
* [Bria Background Replace](https://fal.ai/docs/model-api-reference/image-generation-api/bria-background-replace) — Image Generation
* [Video](https://fal.ai/docs/model-api-reference/video-generation-api/video) — Video Generation
* [Replace Background](https://fal.ai/docs/model-api-reference/image-generation-api/replace-background) — Image Generation
* [Bria Text-to-Image HD](https://fal.ai/docs/model-api-reference/image-generation-api/bria-text-to-image-hd) — Image Generation
* [Bria Text-to-Image Base](https://fal.ai/docs/model-api-reference/image-generation-api/bria-text-to-image-base) — Image Generation
* [Embed Product](https://fal.ai/docs/model-api-reference/image-generation-api/embed-product) — Image Generation
* [Bria Text-to-Image Fast](https://fal.ai/docs/model-api-reference/image-generation-api/bria-text-to-image-fast) — Image Generation

## Capabilities

* Image input
* Inpainting (mask support)
* Text prompt input
* Negative prompts
* Reproducible generation (seed)
* Batch generation
* Synchronous mode

## API Reference

### Input Schema

**image\_url** `string` *required*

Input Image to erase from

**mask\_url** `string` *required*

The URL of the binary mask image that represents the area that will be cleaned.

**prompt** `string` *required*

The prompt you would like to use to generate images.

**negative\_prompt** `string`

The negative prompt you would like to use to generate images. Default value: `""`

**seed** `integer`

The same seed and the same prompt given to the same version of the model
will output the same image every time.

Range: `0` to `2147483647`

**num\_images** `integer` (default: 1)

Number of Images to generate. Default value: `1`

Range: `1` to `4`

**sync\_mode** `boolean` (default: false)

If `True`, the media will be returned as a data URI and the output data won't be available in the request history.

### Output Schema

**images** `list\<Image\>` *required*

Generated Images

**seed** `integer` *required*

Seed value used for generation.

## Input Example

```json theme={null}
{
  "image_url": "https://storage.googleapis.com/falserverless/bria/bria_genfill_img.png",
  "mask_url": "https://storage.googleapis.com/falserverless/bria/bria_genfill_mask.png",
  "prompt": "A red delicious cherry",
  "negative_prompt": "",
  "num_images": 1,
  "sync_mode": false
}
```

## Output Example

```json theme={null}
{
  "images": [
    {
      "content_type": "image/png",
      "file_name": "a0d138e6820c4ad58f1fd3c758f16047.png",
      "file_size": 1064550,
      "height": 768,
      "url": "https://storage.googleapis.com/falserverless/bria/bria_genfill_res.png",
      "width": 1024
    }
  ]
}
```

## Limitations

* `seed` range: 0 to 2147483647
* `num_images` range: 1 to 4
