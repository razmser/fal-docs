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

# Bria Fibo Lite API

> API reference for Bria Fibo Lite. Fibo Lite, the new addition to the Fibo model family, allows generating high-quality images with the same controllability of the JSON structured prompt with significa

**Endpoint:** `POST https://fal.run/bria/fibo-lite/generate`
**Endpoint ID:** `bria/fibo-lite/generate`

> [card] [Try it in the Playground](https://fal.ai/models/bria/fibo-lite/generate/playground)
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
    "bria/fibo-lite/generate",
    arguments={},
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("bria/fibo-lite/generate", {
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
  --url https://fal.run/bria/fibo-lite/generate \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{}'
```

## Examples

> several coffee beans floating independently in mid air, the subject is red, in a red photo, red and black duotone image, red wash, red light, red lens, extreme close up, a dark black empty background, closeup, dramatic lighting with a strong red hue that gives the image a moody and intense atmospher...

![Generated image: several coffee beans floating independently in mid air, the subject is red, in a](https://v3b.fal.media/files/b/0a8739b1/5PkOKEpdQu16ZkS3ovA92_e0d9e8ab6833478aa854cba5ca1c88ad.png)

![Example output from Fibo Lite](https://v3b.fal.media/files/b/0a873af8/6YpzosRTtGfE40Iw28xvo_99cc31c90a474a42a3b64be2b1e6aacd.png)

![Example output from Fibo Lite](https://v3b.fal.media/files/b/0a873aed/b_8ygdKfq7HQnL5s8PD3J_5c4a1f3dca65408ba7bbc9cb45b1688a.png)

### Input Schema

**prompt** `string`

The prompt to generate.

**structured\_prompt** `StructuredPrompt`

The structured prompt to generate.

**image\_url** `string`

Input image URL

**seed** `integer` (default: 7)

Seed for the random number generator. Default value: `7`

**aspect\_ratio** `AspectRatioEnum` (default: 1:1)

Aspect ratio. Options: 1:1, 2:3, 3:2, 3:4, 4:3, 4:5, 5:4, 9:16, 16:9 Default value: `"1:1"`

Possible values: `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`

**negative\_prompt** `string`

Negative prompt for image generation. Default value: `""`

**steps\_num** `integer` (default: 8)

Number of inference steps. Default value: `8`

Range: `4` to `30`

**sync\_mode** `boolean` (default: false)

If true, returns the image directly in the response (increases latency).

### Output Schema

**image** `Image` *required*

Generated image.

**images** `list\<object\>`

Generated images.

**structured\_prompt** `StructuredPrompt` *required*

Current prompt.

### Input Example

```json theme={null}
{
  "prompt": "Oil painting of a fluffy, wide-eyed cat sitting upright, holding a small wooden sign reading \"Feed Me.\" Rich textures, dramatic brushstrokes, warm tones, and vintage charm.",
  "seed": 7,
  "aspect_ratio": "1:1",
  "negative_prompt": "",
  "steps_num": 8,
  "sync_mode": false
}
```

### Output Example

```json theme={null}
{
  "image": {
    "url": "",
    "content_type": "image/png",
    "file_name": "z9RV14K95DvU.png",
    "file_size": 4404019,
    "width": 1024,
    "height": 1024
  },
  "structured_prompt": {
    "short_description": "",
    "objects": [
      {
        "description": "",
        "location": "",
        "relationship": ""
      }
    ],
    "background_setting": "",
    "lighting": {
      "conditions": "",
      "direction": ""
    },
    "aesthetics": {
      "composition": "",
      "color_scheme": "",
      "mood_atmosphere": "",
      "aesthetic_score": "",
      "preference_score": ""
    },
    "text_render": [
      {
        "text": "",
        "location": "",
        "size": "",
        "color": "",
        "font": ""
      }
    ],
    "context": "",
    "artistic_style": ""
  }
}
```

**Overview**

Fibo Lite, the new addition to the Fibo model family, allows generating **high-quality** images with the same **controllability** of the JSON structured prompt, while using significantly fewer inference steps, resulting in significantly **improved latency.**

This is a two-stage distilled model for the [FIBO](https://huggingface.co/briaai/FIBO) text-to-image model, combining:

1. CFG Distillation: First, we distill classifier-free guidance into the model, enabling inference with Guidance Scale = 1.0 (skipping the negative prompt pass).
2. SCFM (Shortcutting Flow Matching): On top of the CFG-distilled merged model, we apply velocity field self-distillation to enable efficient few-step sampling.

### **🔑 Key Benefits**

* Two-Stage Distillation: Combines CFG distillation with SCFM for maximum efficiency—the SCFM was trained on top of the already CFG-distilled merged model.
* Few-Step Generation: SCFM enables efficient sampling in significantly fewer inference steps.
* No CFG Overhead: Running at guidance\_scale=1 means calculating the noise prediction only once per step instead of twice.
* Quality Tradeoff: As a distillation approach, there is a slight quality degradation compared to the full model at CFG=5, but the speed gains make it ideal for rapid iteration and production workflows.
* Drop-in Replacement: Works seamlessly with existing FIBO workflows—just set guidance\_scale=1.0.
* Memory Efficient: Minimal additional GPU memory overhead.

## Limitations

* `steps_num` range: 4 to 30
