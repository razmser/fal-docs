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

# Flux Pro V1.1 Ultra Finetuned API

> API reference for Flux Pro V1.1 Ultra Finetuned. FLUX1.1 [pro] ultra fine-tuned is the newest version of FLUX1.1 [pro] with a fine-tuned LoRA, maintaining professional-grade image quality while delive

**Endpoint:** `POST https://fal.run/fal-ai/flux-pro/v1.1-ultra-finetuned`
**Endpoint ID:** `fal-ai/flux-pro/v1.1-ultra-finetuned`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/flux-pro/v1.1-ultra-finetuned/playground)
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
    "fal-ai/flux-pro/v1.1-ultra-finetuned",
    arguments={
        "prompt": "Extreme close-up of a single tiger eye, direct frontal view. Detailed iris and pupil. Sharp focus on eye texture and color. Natural lighting to capture authentic eye shine and depth. The word \"FLUX\" is painted over it in big, white brush strokes with visible texture.",
        "finetune_id": ""
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/flux-pro/v1.1-ultra-finetuned", {
  input: {
      prompt: "Extreme close-up of a single tiger eye, direct frontal view. Detailed iris and pupil. Sharp focus on eye texture and color. Natural lighting to capture authentic eye shine and depth. The word \"FLUX\" is painted over it in big, white brush strokes with visible texture.",
      finetune_id: ""
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
  --url https://fal.run/fal-ai/flux-pro/v1.1-ultra-finetuned \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "prompt": "Extreme close-up of a single tiger eye, direct frontal view. Detailed iris and pupil. Sharp focus on eye texture and color. Natural lighting to capture authentic eye shine and depth. The word \"FLUX\" is painted over it in big, white brush strokes with visible texture.",
  "finetune_id": ""
}'
```

## Related

* [FLUX1.1 \[pro\] ultra](https://fal.ai/docs/model-api-reference/image-generation-api/flux1.1-ultra) — Image Generation
* [FLUX1.1 \[pro\]](https://fal.ai/docs/model-api-reference/image-generation-api/flux1.1) — Image Generation
* [FLUX.1 \[pro\] Fill](https://fal.ai/docs/model-api-reference/image-generation-api/flux.1-fill) — Image Generation
* [FLUX1.1 \[pro\] Redux](https://fal.ai/docs/model-api-reference/image-generation-api/flux1.1-redux) — Image Generation
* [FLUX1.1 \[pro\] ultra Redux](https://fal.ai/docs/model-api-reference/image-generation-api/flux1.1-ultra-redux) — Image Generation
* [FLUX.1 \[pro\] Fill Fine-tuned](https://fal.ai/docs/model-api-reference/image-generation-api/flux.1-fill-fine-tuned) — Image Generation

## Capabilities

* Text prompt input
* Reproducible generation (seed)
* Synchronous mode
* Batch generation
* Image input
* Aspect ratio control

## API Reference

### Input Schema

**prompt** `string` *required*

The prompt to generate an image from.

**seed** `integer`

The same seed and the same prompt given to the same version of the model
will output the same image every time.

**sync\_mode** `boolean` (default: false)

If `True`, the media will be returned as a data URI and the output data won't be available in the request history.

**num\_images** `integer` (default: 1)

The number of images to generate. Default value: `1`

Range: `1` to `4`

**output\_format** `OutputFormatEnum` (default: jpeg)

The format of the generated image. Default value: `"jpeg"`

Possible values: `jpeg`, `png`

**safety\_tolerance** `SafetyToleranceEnum` (default: 2)

The safety tolerance level for the generated image. 1 being the most strict and 5 being the most permissive. Default value: `"2"`

Possible values: `1`, `2`, `3`, `4`, `5`, `6`

**enhance\_prompt** `boolean` (default: false)

Whether to enhance the prompt for better results.

**image\_url** `string`

The image URL to generate an image from.

**image\_prompt\_strength** `float` (default: 0.1)

The strength of the image prompt, between 0 and 1. Default value: `0.1`

Range: `0` to `1`

**aspect\_ratio** `Enum | string` (default: 16:9)

The aspect ratio of the generated image. Default value: `16:9`

Possible values: `21:9`, `16:9`, `4:3`, `3:2`, `1:1`, `2:3`, `3:4`, `9:16`, `9:21`

**raw** `boolean` (default: false)

Generate less processed, more natural-looking images.

**finetune\_id** `string` *required*

References your specific model

**finetune\_strength** `float` *required*

Controls finetune influence.
Increase this value if your target concept isn't showing up strongly enough.
The optimal setting depends on your finetune and prompt

Range: `0` to `2`

### Output Schema

**images** `list\<registry__image__fast_sdxl__models__Image\>` *required*

The generated image files info.

**timings** `Timings` *required*

**seed** `integer` *required*

Seed of the generated Image. It will be the same value of the one passed in the
input or the randomly generated that was used in case none was passed.

**has\_nsfw\_concepts** `list\<boolean\>` *required*

Whether the generated images contain NSFW concepts.

**prompt** `string` *required*

The prompt used for generating the image.

## Input Example

```json theme={null}
{
  "prompt": "Extreme close-up of a single tiger eye, direct frontal view. Detailed iris and pupil. Sharp focus on eye texture and color. Natural lighting to capture authentic eye shine and depth. The word \"FLUX\" is painted over it in big, white brush strokes with visible texture.",
  "sync_mode": false,
  "num_images": 1,
  "output_format": "jpeg",
  "safety_tolerance": "2",
  "enhance_prompt": false,
  "image_prompt_strength": 0.1,
  "aspect_ratio": "16:9",
  "raw": false,
  "finetune_id": ""
}
```

## Output Example

```json theme={null}
{
  "images": [
    {
      "url": "",
      "content_type": "image/jpeg"
    }
  ],
  "prompt": ""
}
```

## Limitations

* `num_images` range: 1 to 4
* `output_format` restricted to: `jpeg`, `png`
* `safety_tolerance` restricted to: `1`, `2`, `3`, `4`, `5`, `6`
* `image_prompt_strength` range: 0 to 1
* `finetune_strength` range: 0 to 2
