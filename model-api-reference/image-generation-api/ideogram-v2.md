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

# Ideogram V2 API

> API reference for Ideogram V2. Generate high-quality images, posters, and logos with Ideogram V2. Features exceptional typography handling and realistic outputs optimized for commercial and creative u

**Endpoint:** `POST https://fal.run/fal-ai/ideogram/v2`
**Endpoint ID:** `fal-ai/ideogram/v2`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/ideogram/v2/playground)
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
    "fal-ai/ideogram/v2",
    arguments={
        "prompt": "A comic style illustration of a skeleton sitting on a toilet in a bathroom. The bathroom has a Halloween decoration with a pumpkin jack-o-lantern and bats flying around. There is a text above the skeleton that says \"Just Waiting for Halloween with Ideogram 2.0 at fal.ai\""
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/ideogram/v2", {
  input: {
      prompt: "A comic style illustration of a skeleton sitting on a toilet in a bathroom. The bathroom has a Halloween decoration with a pumpkin jack-o-lantern and bats flying around. There is a text above the skeleton that says \"Just Waiting for Halloween with Ideogram 2.0 at fal.ai\""
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
  --url https://fal.run/fal-ai/ideogram/v2 \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "prompt": "A comic style illustration of a skeleton sitting on a toilet in a bathroom. The bathroom has a Halloween decoration with a pumpkin jack-o-lantern and bats flying around. There is a text above the skeleton that says \"Just Waiting for Halloween with Ideogram 2.0 at fal.ai\""
}'
```

## Examples

> A playful cat is sitting on the right side of a vibrant beach ball, situated on a sunny, sandy beach. The beach is dotted with sunbathers and colorful umbrellas. Waves gently lap at the shore, and a pristine blue sky is adorned with a few fluffy white clouds. Seagulls soar overhead, adding to the li...

![Generated image: A playful cat is sitting on the right side of a vibrant beach ball, situated on ](https://fal.media/files/kangaroo/pBa0_WFY5AEO6Q_mnbtNB_image.png)

> A warm, hyper-detailed, low-saturation scene in the style of David Lynch, set against a soft-focus suburban American backdrop with muted colors and soothing tones, evoking a sense of nostalgia. Two characters, a young, ethnically ambiguous woman with sun-kissed, golden-brown skin and a bright, enigm...

![Generated image: A warm, hyper-detailed, low-saturation scene in the style of David Lynch, set ag](https://fal.media/files/koala/dSrkX-nFdB_nDA3NaKkMB_image.png)

> A marble-skinned figure with neoclassical features, matte skin, and wrinkles, majestically strides forward on a serene tropical beach with a palm tree and green foliage, amidst a blurred bustling galactic trade hub background.

![Generated image: A marble-skinned figure with neoclassical features, matte skin, and wrinkles, ma](https://fal.media/files/penguin/xYDHqdCkh_azyfWaY8Lsp_image.png)

## Related

* [Ideogram V2 Remix](ideogram-v2-remix.md) — Image Generation
* [Ideogram V2 Edit](ideogram-v2-edit.md) — Image Generation

## Capabilities

* Text prompt input
* Aspect ratio control
* Reproducible generation (seed)
* Synchronous mode
* Negative prompts

## API Reference

### Input Schema

**prompt** `string` *required*

**aspect\_ratio** `AspectRatioEnum` (default: 1:1)

The aspect ratio of the generated image Default value: `"1:1"`

Possible values: `10:16`, `16:10`, `9:16`, `16:9`, `4:3`, `3:4`, `1:1`, `1:3`, `3:1`, `3:2`, `2:3`

**expand\_prompt** `boolean` (default: true)

Whether to expand the prompt with MagicPrompt functionality. Default value: `true`

**seed** `integer`

Seed for the random number generator

**style** `StyleEnum` (default: auto)

The style of the generated image Default value: `"auto"`

Possible values: `auto`, `general`, `realistic`, `design`, `render_3D`, `anime`

**sync\_mode** `boolean` (default: false)

If `True`, the media will be returned as a data URI and the output data won't be available in the request history.

**negative\_prompt** `string`

A negative prompt to avoid in the generated image Default value: `""`

### Output Schema

**images** `list\<File\>` *required*

**seed** `integer` *required*

Seed used for the random number generator

## Input Example

```json theme={null}
{
  "prompt": "A comic style illustration of a skeleton sitting on a toilet in a bathroom. The bathroom has a Halloween decoration with a pumpkin jack-o-lantern and bats flying around. There is a text above the skeleton that says \"Just Waiting for Halloween with Ideogram 2.0 at fal.ai\"",
  "aspect_ratio": "1:1",
  "expand_prompt": true,
  "style": "auto",
  "sync_mode": false,
  "negative_prompt": ""
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
