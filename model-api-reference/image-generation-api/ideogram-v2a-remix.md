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

# Ideogram V2a Remix API

> API reference for Ideogram V2a Remix. Create variations of existing images with Ideogram V2A Remix while maintaining creative control through prompt guidance.

**Endpoint:** `POST https://fal.run/fal-ai/ideogram/v2a/remix`
**Endpoint ID:** `fal-ai/ideogram/v2a/remix`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/ideogram/v2a/remix/playground)
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
    "fal-ai/ideogram/v2a/remix",
    arguments={
        "prompt": "An ice field in north atlantic",
        "image_url": "https://fal.media/files/lion/FHOx4y4a0ef7Sgmo-sOUR_image.png"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/ideogram/v2a/remix", {
  input: {
      prompt: "An ice field in north atlantic",
      image_url: "https://fal.media/files/lion/FHOx4y4a0ef7Sgmo-sOUR_image.png"
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
  --url https://fal.run/fal-ai/ideogram/v2a/remix \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "prompt": "An ice field in north atlantic",
  "image_url": "https://fal.media/files/lion/FHOx4y4a0ef7Sgmo-sOUR_image.png"
}'
```

## Related

* [Ideogram V2A Turbo](ideogram-v2a-turbo.md) — Image Generation
* [Ideogram V2A](ideogram-v2a.md) — Image Generation
* [Ideogram V2A Turbo Remix](https://fal.ai/docs/model-api-reference/image-generation-api/ideogram-v2a-turbo-remix) — Image Generation

## Capabilities

* Text prompt input
* Image input
* Aspect ratio control
* Strength control
* Reproducible generation (seed)
* Synchronous mode

## API Reference

### Input Schema

**prompt** `string` *required*

The prompt to remix the image with

**image\_url** `string` *required*

The image URL to remix

**aspect\_ratio** `AspectRatioEnum` (default: 1:1)

The aspect ratio of the generated image Default value: `"1:1"`

Possible values: `10:16`, `16:10`, `9:16`, `16:9`, `4:3`, `3:4`, `1:1`, `1:3`, `3:1`, `3:2`, `2:3`

**strength** `float` (default: 0.8)

Strength of the input image in the remix Default value: `0.8`

Range: `0.01` to `1`

**expand\_prompt** `boolean` (default: true)

Whether to expand the prompt with MagicPrompt functionality. Default value: `true`

**seed** `integer`

Seed for the random number generator

**style** `StyleEnum` (default: auto)

The style of the generated image Default value: `"auto"`

Possible values: `auto`, `general`, `realistic`, `design`, `render_3D`, `anime`

**sync\_mode** `boolean` (default: false)

If `True`, the media will be returned as a data URI and the output data won't be available in the request history.

### Output Schema

**images** `list\<File\>` *required*

**seed** `integer` *required*

Seed used for the random number generator

## Input Example

```json theme={null}
{
  "prompt": "An ice field in north atlantic",
  "image_url": "https://fal.media/files/lion/FHOx4y4a0ef7Sgmo-sOUR_image.png",
  "aspect_ratio": "1:1",
  "strength": 0.8,
  "expand_prompt": true,
  "style": "auto",
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

* `strength` range: 0.01 to 1
* `style` restricted to: `auto`, `general`, `realistic`, `design`, `render_3D`, `anime`
