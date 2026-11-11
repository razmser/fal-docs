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

# Ideogram Upscale API

> API reference for Ideogram Upscale. Ideogram Upscale enhances the resolution of the reference image by up to 2X and might enhance the reference image too. Optionally refine outputs with a prompt for g

**Endpoint:** `POST https://fal.run/fal-ai/ideogram/upscale`
**Endpoint ID:** `fal-ai/ideogram/upscale`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/ideogram/upscale/playground)
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
    "fal-ai/ideogram/upscale",
    arguments={
        "image_url": "https://fal.media/files/monkey/e6RtJf_ue0vyWzeiEmTby.png"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/ideogram/upscale", {
  input: {
      image_url: "https://fal.media/files/monkey/e6RtJf_ue0vyWzeiEmTby.png"
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
  --url https://fal.run/fal-ai/ideogram/upscale \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "image_url": "https://fal.media/files/monkey/e6RtJf_ue0vyWzeiEmTby.png"
}'
```

## Capabilities

* Image input
* Text prompt input
* Reproducible generation (seed)
* Synchronous mode

## API Reference

### Input Schema

**image\_url** `string` *required*

The image URL to upscale

**prompt** `string`

The prompt to upscale the image with Default value: `""`

**resemblance** `integer` (default: 50)

The resemblance of the upscaled image to the original image Default value: `50`

Range: `1` to `100`

**detail** `integer` (default: 50)

The detail of the upscaled image Default value: `50`

Range: `1` to `100`

**expand\_prompt** `boolean` (default: false)

Whether to expand the prompt with MagicPrompt functionality.

**seed** `integer`

Seed for the random number generator

**sync\_mode** `boolean` (default: false)

If `True`, the media will be returned as a data URI and the output data won't be available in the request history.

### Output Schema

**images** `list\<File\>` *required*

**seed** `integer` *required*

Seed used for the random number generator

## Input Example

```json theme={null}
{
  "image_url": "https://fal.media/files/monkey/e6RtJf_ue0vyWzeiEmTby.png",
  "prompt": "",
  "resemblance": 50,
  "detail": 50,
  "expand_prompt": false,
  "sync_mode": false
}
```

## Output Example

```json theme={null}
{
  "images": [
    {
      "content_type": "image/png",
      "file_name": "image.png",
      "file_size": 6548418,
      "url": "https://fal.media/files/lion/DxTSV6683MLl4VPAVUHR3_image.png"
    }
  ],
  "seed": 123456
}
```

## Limitations

* `resemblance` range: 1 to 100
* `detail` range: 1 to 100
