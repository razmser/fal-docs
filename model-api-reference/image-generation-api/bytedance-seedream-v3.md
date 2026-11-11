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

# Bytedance Seedream V3 API

> API reference for Bytedance Seedream V3. Seedream 3.0 is a bilingual (Chinese and English) text-to-image model that excels at text-to-image generation.

**Endpoint:** `POST https://fal.run/fal-ai/bytedance/seedream/v3/text-to-image`
**Endpoint ID:** `fal-ai/bytedance/seedream/v3/text-to-image`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/bytedance/seedream/v3/text-to-image/playground)
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
    "fal-ai/bytedance/seedream/v3/text-to-image",
    arguments={
        "prompt": "Fisheye lens, the head of a cat, the image shows the effect that the facial features of the cat are distorted due to the shooting method."
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/bytedance/seedream/v3/text-to-image", {
  input: {
      prompt: "Fisheye lens, the head of a cat, the image shows the effect that the facial features of the cat are distorted due to the shooting method."
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
  --url https://fal.run/fal-ai/bytedance/seedream/v3/text-to-image \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "prompt": "Fisheye lens, the head of a cat, the image shows the effect that the facial features of the cat are distorted due to the shooting method."
}'
```

### Input Schema

**prompt** `string` *required*

The text prompt used to generate the image

**image\_size** `ImageSize | Enum`

Use for finer control over the output image size. Will be used over aspect\_ratio, if both are provided. Width and height must be between 512 and 2048.

Possible values: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`

**guidance\_scale** `float` (default: 2.5)

Controls how closely the output image aligns with the input prompt. Higher values mean stronger prompt correlation. Default value: `2.5`

Range: `1` to `10`

**num\_images** `integer` (default: 1)

Number of images to generate Default value: `1`

Range: `1` to `4`

**seed** `integer`

Random seed to control the stochasticity of image generation.

**enable\_safety\_checker** `boolean` (default: true)

If set to true, the safety checker will be enabled. Default value: `true`

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
  "prompt": "Fisheye lens, the head of a cat, the image shows the effect that the facial features of the cat are distorted due to the shooting method.",
  "guidance_scale": 2.5,
  "num_images": 1,
  "enable_safety_checker": true,
  "sync_mode": false
}
```

### Output Example

```json theme={null}
{
  "images": [
    {
      "url": "https://v3.fal.media/files/rabbit/EJqemc4hQlHKAtkkfTJqB_a2aaccab7ff84740b6323da580146087.png"
    }
  ],
  "seed": 42
}
```

## Limitations

* `image_size` restricted to: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`
* `guidance_scale` range: 1 to 10
* `num_images` range: 1 to 4
* Content moderation via safety checker
