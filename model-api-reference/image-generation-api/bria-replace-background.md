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

# Bria Replace Background API

> API reference for Bria Replace Background. Creates enriched product shots by placing them in various environments using textual descriptions.

**Endpoint:** `POST https://fal.run/bria/replace-background`
**Endpoint ID:** `bria/replace-background`

> [card] [Try it in the Playground](https://fal.ai/models/bria/replace-background/playground)
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
    "bria/replace-background",
    arguments={},
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("bria/replace-background", {
  input: {},
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
  --url https://fal.run/bria/replace-background \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{}'
```

## Related

* [Bria RMBG 2.0](https://fal.ai/docs/model-api-reference/image-generation-api/bria-rmbg-2.0) — Image Generation
* [Bria Expand Image](https://fal.ai/docs/model-api-reference/image-generation-api/bria-expand-image) — Image Generation
* [Bria Eraser](bria-eraser.md) — Image Generation
* [Bria Product Shot](bria-product-shot.md) — Image Generation
* [Bria Background Replace](https://fal.ai/docs/model-api-reference/image-generation-api/bria-background-replace) — Image Generation
* [Video](https://fal.ai/docs/model-api-reference/video-generation-api/video) — Video Generation
* [Bria GenFill](bria-genfill.md) — Image Generation
* [Bria Text-to-Image HD](https://fal.ai/docs/model-api-reference/image-generation-api/bria-text-to-image-hd) — Image Generation
* [Bria Text-to-Image Base](https://fal.ai/docs/model-api-reference/image-generation-api/bria-text-to-image-base) — Image Generation
* [Embed Product](https://fal.ai/docs/model-api-reference/image-generation-api/embed-product) — Image Generation
* [Bria Text-to-Image Fast](https://fal.ai/docs/model-api-reference/image-generation-api/bria-text-to-image-fast) — Image Generation

## Capabilities

* Text prompt input
* Image input
* Reproducible generation (seed)
* Negative prompts
* Synchronous mode

## API Reference

### Input Schema

**prompt** `string`

Prompt for background replacement.

**image\_url** `string` (default: [https://v3b.fal.media/files/b/0a8bea8c/Mztgx0NG3HPdby-4iPqwH\_a\_coffee\_machine\_standing\_in\_the\_kitchen.png](https://v3b.fal.media/files/b/0a8bea8c/Mztgx0NG3HPdby-4iPqwH_a_coffee_machine_standing_in_the_kitchen.png))

Reference image (file or URL). Default value: `"https://v3b.fal.media/files/b/0a8bea8c/Mztgx0NG3HPdby-4iPqwH_a_coffee_machine_standing_in_the_kitchen.png"`

**seed** `integer` (default: 4925634)

Random seed for reproducibility. Default value: `4925634`

**steps\_num** `integer` (default: 30)

Number of inference steps. Default value: `30`

**negative\_prompt** `string`

Negative prompt for background replacement. Default value: `""`

**sync\_mode** `boolean` (default: false)

If true, returns the image directly in the response (increases latency).

### Output Schema

**image** `Image` *required*

Generated image.

**images** `list\<object\>`

Generated images.

## Input Example

```json theme={null}
{
  "prompt": "On a smooth kitchen counter in front of a blue and white patterned ceramic tile wall. A yellow ceramic mug sits to the right. Shot from a straight-on front angle.",
  "image_url": "https://v3b.fal.media/files/b/0a8bea8c/Mztgx0NG3HPdby-4iPqwH_a_coffee_machine_standing_in_the_kitchen.png",
  "seed": 4925634,
  "steps_num": 30,
  "negative_prompt": "",
  "sync_mode": false
}
```

## Output Example

```json theme={null}
{
  "image": {
    "url": "",
    "content_type": "image/png",
    "file_name": "z9RV14K95DvU.png",
    "file_size": 4404019,
    "width": 1024,
    "height": 1024
  }
}
```
