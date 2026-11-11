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

# Bria Reimagine API

> API reference for Bria Reimagine. Structure Reference allows generating new images while preserving the structure of an input image, guided by text prompts. Perfect for transforming sketches, illustra

**Endpoint:** `POST https://fal.run/fal-ai/bria/reimagine`
**Endpoint ID:** `fal-ai/bria/reimagine`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/bria/reimagine/playground)
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
    "fal-ai/bria/reimagine",
    arguments={
        "prompt": "A 2d illustration of a dog in a vibrant park"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/bria/reimagine", {
  input: {
      prompt: "A 2d illustration of a dog in a vibrant park"
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
  --url https://fal.run/fal-ai/bria/reimagine \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "prompt": "A 2d illustration of a dog in a vibrant park"
}'
```

## Capabilities

* Text prompt input
* Reproducible generation (seed)
* Adjustable inference steps
* Synchronous mode

## API Reference

### Input Schema

**prompt** `string` *required*

The prompt you would like to use to generate images.

**structure\_image\_url** `string`

The URL of the structure reference image. Use "" to leave empty. Accepted formats are jpeg, jpg, png, webp. Default value: `""`

**structure\_ref\_influence** `float` (default: 0.75)

The influence of the structure reference on the generated image. Default value: `0.75`

**num\_results** `integer` (default: 1)

How many images you would like to generate. When using any Guidance Method, Value is set to 1. Default value: `1`

Range: `1` to `4`

**seed** `integer`

The same seed and the same prompt given to the same version of the model
will output the same image every time.

Range: `0` to `2147483647`

**fast** `boolean` (default: true)

Whether to use the fast model Default value: `true`

**num\_inference\_steps** `integer` (default: 30)

The number of iterations the model goes through to refine the generated image. This parameter is optional. Default value: `30`

Range: `20` to `50`

**sync\_mode** `boolean` (default: false)

If `True`, the media will be returned as a data URI and the output data won't be available in the request history.

### Output Schema

**images** `list\<Image\>` *required*

The generated images

**seed** `integer` *required*

Seed value used for generation.

## Input Example

```json theme={null}
{
  "prompt": "A 2d illustration of a dog in a vibrant park",
  "structure_image_url": "https://storage.googleapis.com/falserverless/bria/bria_reimagine_input.png",
  "structure_ref_influence": 0.15,
  "num_results": 1,
  "fast": true,
  "num_inference_steps": 30,
  "sync_mode": false
}
```

## Output Example

```json theme={null}
{
  "images": [
    {
      "content_type": "image/png",
      "url": "https://storage.googleapis.com/falserverless/bria/bria_reimagine_output.png"
    }
  ]
}
```

## Limitations

* `num_results` range: 1 to 4
* `seed` range: 0 to 2147483647
* `num_inference_steps` range: 20 to 50
