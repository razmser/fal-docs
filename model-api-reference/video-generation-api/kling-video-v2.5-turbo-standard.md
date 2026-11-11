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

# Kling Video V2.5 Turbo Standard API

> API reference for Kling Video V2.5 Turbo Standard. Kling 2.5 Turbo Standard: Top-tier image-to-video generation with unparalleled motion fluidity, cinematic visuals, and exceptional prompt precision.

**Endpoint:** `POST https://fal.run/fal-ai/kling-video/v2.5-turbo/standard/image-to-video`
**Endpoint ID:** `fal-ai/kling-video/v2.5-turbo/standard/image-to-video`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/v2.5-turbo/standard/image-to-video/playground)
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
    "fal-ai/kling-video/v2.5-turbo/standard/image-to-video",
    arguments={
        "prompt": "In a dimly lit room, a playful cat's eyes light up, fixated on a dancing red dot. With boundless energy, it pounces and leaps, chasing the elusive beam across the floor and up the walls. The simple joy of the hunt unfolds in clear, uncomplicated visuals.",
        "image_url": "https://storage.googleapis.com/falserverless/example_inputs/kling_v25_std_i2v_input.png"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/kling-video/v2.5-turbo/standard/image-to-video", {
  input: {
      prompt: "In a dimly lit room, a playful cat's eyes light up, fixated on a dancing red dot. With boundless energy, it pounces and leaps, chasing the elusive beam across the floor and up the walls. The simple joy of the hunt unfolds in clear, uncomplicated visuals.",
      image_url: "https://storage.googleapis.com/falserverless/example_inputs/kling_v25_std_i2v_input.png"
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
  --url https://fal.run/fal-ai/kling-video/v2.5-turbo/standard/image-to-video \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "prompt": "In a dimly lit room, a playful cat'\''s eyes light up, fixated on a dancing red dot. With boundless energy, it pounces and leaps, chasing the elusive beam across the floor and up the walls. The simple joy of the hunt unfolds in clear, uncomplicated visuals.",
  "image_url": "https://storage.googleapis.com/falserverless/example_inputs/kling_v25_std_i2v_input.png"
}'
```

### Input Schema

**prompt** `string` *required*

**image\_url** `string` *required*

URL of the image to be used for the video

**duration** `DurationEnum` (default: 5)

The duration of the generated video in seconds Default value: `"5"`

Possible values: `5`, `10`

**negative\_prompt** `string` (default: blur, distort, and low quality)

Default value: `"blur, distort, and low quality"`

**cfg\_scale** `float` (default: 0.5)

The CFG (Classifier Free Guidance) scale is a measure of how close you want
the model to stick to your prompt. Default value: `0.5`

Range: `0` to `1`

### Output Schema

**video** `File` *required*

The generated video

### Input Example

```json theme={null}
{
  "prompt": "In a dimly lit room, a playful cat's eyes light up, fixated on a dancing red dot. With boundless energy, it pounces and leaps, chasing the elusive beam across the floor and up the walls. The simple joy of the hunt unfolds in clear, uncomplicated visuals.",
  "image_url": "https://storage.googleapis.com/falserverless/example_inputs/kling_v25_std_i2v_input.png",
  "duration": "5",
  "negative_prompt": "blur, distort, and low quality",
  "cfg_scale": 0.5
}
```

### Output Example

```json theme={null}
{
  "video": {
    "url": "https://storage.googleapis.com/falserverless/example_outputs/kling_v25_std_i2v_output.mp4"
  }
}
```

## Related

* [Kling Video](https://fal.ai/docs/model-api-reference/video-generation-api/kling-video) — Video Generation
* [Kling v2.5 Text to Video](https://fal.ai/docs/model-api-reference/video-generation-api/kling-v2.5-text-to-video) — Video Generation

## Limitations

* `duration` restricted to: `5`, `10`
* `cfg_scale` range: 0 to 1
