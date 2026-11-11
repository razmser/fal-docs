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

# Bytedance Dreamina V3.1 API

> API reference for Bytedance Dreamina V3.1. Dreamina showcases superior picture effects, with significant improvements in picture aesthetics, precise and diverse styles, and rich details.

**Endpoint:** `POST https://fal.run/fal-ai/bytedance/dreamina/v3.1/text-to-image`
**Endpoint ID:** `fal-ai/bytedance/dreamina/v3.1/text-to-image`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/bytedance/dreamina/v3.1/text-to-image/playground)
>
> Run this model interactively with your own prompts.

### Quick Start

```python title="Python" theme={null}
import fal_client

def on_queue_update(update):
    if isinstance(update, fal_client.InProgress):
        for log in update.logs:
           print(log["message"])

result = fal_client.subscribe(
    "fal-ai/bytedance/dreamina/v3.1/text-to-image",
    arguments={
        "prompt": "A 25-year-old korean woman selfie, front facing camera, lighting is soft and natural. If background is visible, it's a clean, modern apartment interior. The clothing color is clearly visible and distinct, adding a hint of color contrast"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/bytedance/dreamina/v3.1/text-to-image", {
  input: {
      prompt: "A 25-year-old korean woman selfie, front facing camera, lighting is soft and natural. If background is visible, it's a clean, modern apartment interior. The clothing color is clearly visible and distinct, adding a hint of color contrast"
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
  --url https://fal.run/fal-ai/bytedance/dreamina/v3.1/text-to-image \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "prompt": "A 25-year-old korean woman selfie, front facing camera, lighting is soft and natural. If background is visible, it'\''s a clean, modern apartment interior. The clothing color is clearly visible and distinct, adding a hint of color contrast"
}'
```

### Input Schema

**prompt** `string` *required*

The text prompt used to generate the image

**image\_size** `ImageSize | Enum` (default: \[object Object])

The size of the generated image. Width and height must be between 512 and 2048.

Possible values: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`

**enhance\_prompt** `boolean` (default: false)

Whether to use an LLM to enhance the prompt

**num\_images** `integer` (default: 1)

Number of images to generate Default value: `1`

Range: `1` to `4`

**seed** `integer`

Random seed to control the stochasticity of image generation.

**sync\_mode** `boolean` (default: false)

If `True`, the media will be returned as a data URI and the output data won't be available in the request history.

### Output Schema

**images** `list\<Image\>` *required*

Generated images

**seed** `integer` *required*

Seed used for generation

### Input Example

```json theme={null}
{
  "prompt": "A 25-year-old korean woman selfie, front facing camera, lighting is soft and natural. If background is visible, it's a clean, modern apartment interior. The clothing color is clearly visible and distinct, adding a hint of color contrast",
  "image_size": {
    "width": 2048,
    "height": 1536
  },
  "enhance_prompt": false,
  "num_images": 1,
  "sync_mode": false
}
```

### Output Example

```json theme={null}
{
  "images": [
    {
      "url": "https://v3.fal.media/files/panda/4mddd7PmDvbbBZDs-xnUW_4294a9041c9d46eaa7b98d15ce6300fb.png"
    }
  ],
  "seed": 746406749
}
```

## Related

* [Bytedance Seedream v4 Edit](https://fal.ai/docs/model-api-reference/image-generation-api/bytedance-seedream-v4-edit) — Image Generation
* [Bytedance Seedream v4](bytedance-seedream-v4.md) — Image Generation

## Limitations

* `image_size` restricted to: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`
* `num_images` range: 1 to 4
