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

# Bria Fibo API

> API reference for Bria Fibo. SOTA Open source model trained on licensed data, transforming intent into structured control for precise, high-quality AI image generation in enterprise and agentic workfl

**Endpoint:** `POST https://fal.run/bria/fibo/generate`
**Endpoint ID:** `bria/fibo/generate`

> [card] [Try it in the Playground](https://fal.ai/models/bria/fibo/generate/playground)
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
    "bria/fibo/generate",
    arguments={},
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("bria/fibo/generate", {
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
  --url https://fal.run/bria/fibo/generate \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{}'
```

## Examples

> 3D animated anthropomorphic fox character, adventurous hero pose, orange and white fur with teal accents, wearing leather adventure vest with pouches, confident expression, stylized proportions, standing in T-pose, neutral gray background, game-ready character design, cel-shaded rendering style

![Generated image: 3D animated anthropomorphic fox character, adventurous hero pose, orange and whi](https://v3b.fal.media/files/b/rabbit/SVbriPvB6AgcT6ZgoA8ci_e3d0d38608a443cc81dd1ba138e6c985.png)

> A hyper-detailed, ultra-fluffy owl sitting in the trees at night, looking directly at the camera with wide, adorable, expressive eyes. Its feathers are soft and voluminous, catching the cool moonlight with subtle silver highlights. The owl's gaze is curious and full of charm, giving it a whimsical, ...

![Generated image: A hyper-detailed, ultra-fluffy owl sitting in the trees at night, looking direct](https://v3b.fal.media/files/b/penguin/96Py3o4WxTC-hErIb4yRC_be2f4924640c46fe8ce2277eab6fb22d.png)

> A glowing jack-o’-lantern carved from a rich orange pumpkin, its jagged grin and triangular eyes illuminated by a warm flickering candle inside. The surface shows natural texture and faint imperfections, with soft shadows cast on the ground. Set in a dimly lit autumn scene with fallen leaves, cool m...

![Generated image: A glowing jack-o’-lantern carved from a rich orange pumpkin, its jagged grin and](https://v3b.fal.media/files/b/lion/5WUu7709UwZxX8tuzfCyi_4d984ad95a444ef8aa4bfcb7d19b2352.png)

### Input Schema

**prompt** `string`

Prompt for image generation.

**structured\_prompt** `StructuredPrompt`

The structured prompt to generate an image from.

**image\_url** `string`

Reference image (file or URL).

**seed** `integer` (default: 5555)

Random seed for reproducibility. Default value: `5555`

**steps\_num** `integer` (default: 50)

Number of inference steps. Default value: `50`

Range: `20` to `50`

**aspect\_ratio** `AspectRatioEnum` (default: 1:1)

Aspect ratio. Options: 1:1, 2:3, 3:2, 3:4, 4:3, 4:5, 5:4, 9:16, 16:9 Default value: `"1:1"`

Possible values: `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`

**negative\_prompt** `string`

Negative prompt for image generation. Default value: `""`

**resolution** `ResolutionEnum` (default: 1MP)

Output image resolution Default value: `"1MP"`

Possible values: `1MP`, `4MP`

**sync\_mode** `boolean` (default: false)

If true, returns the image directly in the response (increases latency).

### Output Schema

**image** `Image` *required*

Generated image.

**images** `list\<object\>`

Generated images.

**structured\_prompt** `Structured Prompt` *required*

Current prompt.

### Input Example

```json theme={null}
{
  "prompt": "A hyper-detailed, ultra-fluffy owl sitting in the trees at night, looking directly at the camera with wide, adorable, expressive eyes. Its feathers are soft and voluminous, catching the cool moonlight with subtle silver highlights. The owl’s gaze is curious and full of charm, giving it a whimsical, storybook-like personality.",
  "seed": 5555,
  "steps_num": 50,
  "aspect_ratio": "1:1",
  "negative_prompt": "",
  "resolution": "1MP",
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
  "images": []
}
```

Text-to-image models have mastered imagination - but not control. FIBO changes that.
FIBO is trained on structured JSON captions up to 1,000+ words and designed to understand and control different visual parameters such as lighting, composition, color, and camera settings, enabling precise and reproducible outputs.
With only 8 billion parameters, FIBO provides a new level of image quality, prompt adherence and professional control. Enterprises demand repeatability, governance, and transparency. FIBO delivers - open-source, rights-clear, and ready for production.

# 🔑 Key Features

* LLM guided JSON-native prompting: structured schemas up to 1,000+ words (lighting, camera, composition, DoF)
* Iterative controlled generation: generate images from short prompts or keep refining and get inspiration from detailed JSONs and input images
* Disentangled control: tweak a single attribute (e.g., camera angle) without breaking the scene.
* Enterprise-grade: 100% licensed data; governance, repeatability, and legal clarity.
* Strong prompt adherence: high alignment on PRISM-style evaluations.

# Benchmark:

![benchmark](https://v3b.fal.media/files/b/kangaroo/ISYj3rTKlV1LvG76ZGJcb_Screenshot%202025-10-29%20at%207.54.17%3FAM.png)

# 💡 What can you do with FIBO?

## Generate

Start with a quick idea. FIBO’s language model expands your short prompt into a rich, structured JSON prompt, then generates the image. You get both the image and the expanded prompt.

## Refine

Continue from a detailed structured prompt and add a small instruction - for example, “backlit,” “85 mm,” or “warmer skin tones.” FIBO updates only the requested attributes, re-generates the image, and returns the refined prompt alongside it.

## Inspire

Provide an image instead of text. FIBO’s vision-language model extracts a detailed prompt describing it, combines that with your creative intent, and generates new, related images - ideal for exploring new directions without altering the original.

## Limitations

* `steps_num` range: 20 to 50
* `resolution` restricted to: `1MP`, `4MP`
