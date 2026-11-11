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

# Bria Expand API

> API reference for Bria Expand. Bria Expand expands images beyond their borders in high quality. Trained exclusively on licensed data for safe and risk-free commercial use. Access the model's source co

**Endpoint:** `POST https://fal.run/fal-ai/bria/expand`
**Endpoint ID:** `fal-ai/bria/expand`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/bria/expand/playground)
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
    "fal-ai/bria/expand",
    arguments={
        "image_url": "https://storage.googleapis.com/falserverless/model_tests/orange.png",
        "canvas_size": [
            1200,
            674
        ]
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/bria/expand", {
  input: {
      image_url: "https://storage.googleapis.com/falserverless/model_tests/orange.png",
      canvas_size: [
        1200,
        674
      ]
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
  --url https://fal.run/fal-ai/bria/expand \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "image_url": "https://storage.googleapis.com/falserverless/model_tests/orange.png",
  "canvas_size": [
    1200,
    674
  ]
}'
```

## Related

* [Bria RMBG 2.0](https://fal.ai/docs/model-api-reference/image-generation-api/bria-rmbg-2.0) — Image Generation
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

## Capabilities

* Image input
* Aspect ratio control
* Text prompt input
* Reproducible generation (seed)
* Negative prompts
* Synchronous mode

## API Reference

### Input Schema

**image\_url** `string` *required*

The URL of the input image.

**canvas\_size** `list\<integer\>` *required*

The desired size of the final image, after the expansion. should have an area of less than 5000x5000 pixels.

**aspect\_ratio** `Enum`

The desired aspect ratio of the final image. Will be used over original\_image\_size and original\_image\_location if provided.

Possible values: `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`

**original\_image\_size** `list\<integer\>`

The desired size of the original image, inside the full canvas. Ensure that the ratio of input image foreground or main subject to the canvas area is greater than 15% to achieve optimal results. Will be ignored if aspect\_ratio is provided.

**original\_image\_location** `list\<integer\>`

The desired location of the original image, inside the full canvas. Provide the location of the upper left corner of the original image. The location can also be outside the canvas (the original image will be cropped). Will be ignored if aspect\_ratio is provided.

**prompt** `string`

Text on which you wish to base the image expansion. This parameter is optional. Bria currently supports prompts in English only, excluding special characters. Default value: `""`

**seed** `integer`

You can choose whether you want your generated expension to be random or predictable. You can recreate the same result in the future by using the seed value of a result from the response. You can exclude this parameter if you are not interested in recreating your results. This parameter is optional.

Range: `0` to `2147483647`

**negative\_prompt** `string`

The negative prompt you would like to use to generate images. Default value: `""`

**sync\_mode** `boolean` (default: false)

If `True`, the media will be returned as a data URI and the output data won't be available in the request history.

### Output Schema

**image** `Image` *required*

The generated image

**seed** `integer` *required*

Seed value used for generation.

## Input Example

```json theme={null}
{
  "image_url": "https://storage.googleapis.com/falserverless/model_tests/orange.png",
  "canvas_size": [
    1200,
    674
  ],
  "original_image_size": [
    610,
    855
  ],
  "original_image_location": [
    301,
    -66
  ],
  "prompt": "",
  "negative_prompt": "",
  "sync_mode": false
}
```

## Output Example

```json theme={null}
{
  "image": {
    "content_type": "image/png",
    "file_name": "afa402a35ea742cdb5c3e219b2b19bfb.png",
    "file_size": 1471342,
    "height": 674,
    "url": "https://v3.fal.media/files/koala/8np-spgxxG-I1r3cjthRV_afa402a35ea742cdb5c3e219b2b19bfb.png",
    "width": 1200
  }
}
```

## Limitations

* `seed` range: 0 to 2147483647
