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

# Ideogram V2 Edit API

> API reference for Ideogram V2 Edit. Transform existing images with Ideogram V2's editing capabilities. Modify, adjust, and refine images while maintaining high fidelity and realistic outputs with prec

**Endpoint:** `POST https://fal.run/fal-ai/ideogram/v2/edit`
**Endpoint ID:** `fal-ai/ideogram/v2/edit`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/ideogram/v2/edit/playground)
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
    "fal-ai/ideogram/v2/edit",
    arguments={
        "prompt": "A knight in shining armour holding a greatshield with \"FAL\" on it",
        "image_url": "https://storage.googleapis.com/falserverless/flux-lora/example-images/knight.jpeg",
        "mask_url": "https://storage.googleapis.com/falserverless/flux-lora/example-images/mask_knight.jpeg"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/ideogram/v2/edit", {
  input: {
      prompt: "A knight in shining armour holding a greatshield with \"FAL\" on it",
      image_url: "https://storage.googleapis.com/falserverless/flux-lora/example-images/knight.jpeg",
      mask_url: "https://storage.googleapis.com/falserverless/flux-lora/example-images/mask_knight.jpeg"
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
  --url https://fal.run/fal-ai/ideogram/v2/edit \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "prompt": "A knight in shining armour holding a greatshield with \"FAL\" on it",
  "image_url": "https://storage.googleapis.com/falserverless/flux-lora/example-images/knight.jpeg",
  "mask_url": "https://storage.googleapis.com/falserverless/flux-lora/example-images/mask_knight.jpeg"
}'
```

## Related

* [Ideogram V2](ideogram-v2.md) — Image Generation
* [Ideogram V2 Remix](ideogram-v2-remix.md) — Image Generation

## Capabilities

* Text prompt input
* Image input
* Inpainting (mask support)
* Reproducible generation (seed)
* Synchronous mode

## API Reference

### Input Schema

**prompt** `string` *required*

The prompt to fill the masked part of the image.

**image\_url** `string` *required*

The image URL to generate an image from. Needs to match the dimensions of the mask.

**mask\_url** `string` *required*

The mask URL to inpaint the image. Needs to match the dimensions of the input image.

**seed** `integer`

Seed for the random number generator

**style** `StyleEnum` (default: auto)

The style of the generated image Default value: `"auto"`

Possible values: `auto`, `general`, `realistic`, `design`, `render_3D`, `anime`

**expand\_prompt** `boolean` (default: true)

Whether to expand the prompt with MagicPrompt functionality. Default value: `true`

**sync\_mode** `boolean` (default: false)

If `True`, the media will be returned as a data URI and the output data won't be available in the request history.

### Output Schema

**images** `list\<File\>` *required*

**seed** `integer` *required*

Seed used for the random number generator

## Input Example

```json theme={null}
{
  "prompt": "A knight in shining armour holding a greatshield with \"FAL\" on it",
  "image_url": "https://storage.googleapis.com/falserverless/flux-lora/example-images/knight.jpeg",
  "mask_url": "https://storage.googleapis.com/falserverless/flux-lora/example-images/mask_knight.jpeg",
  "style": "auto",
  "expand_prompt": true,
  "sync_mode": false
}
```

## Output Example

```json theme={null}
{
  "images": [
    {
      "url": "https://fal.media/files/monkey/cNaoxPl0YAWYb-QVBvO9F_image.png"
    }
  ],
  "seed": 123456
}
```

## Limitations

* `style` restricted to: `auto`, `general`, `realistic`, `design`, `render_3D`, `anime`
