> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Common Model Arguments

> Common model inputs and request metadata, including seed, image_size, safety controls, and end-user safety identifiers.

When you call a model on fal, the `arguments` dict (Python) or `input` object (JavaScript) becomes the request body. Over [HTTP](../../api-reference/inference-api.md), you send the same fields as the JSON body. Most fields are model inputs. fal also reads some fields as request metadata, such as the `user` safety identifier. Each model defines its own input schema. You can find that schema on the model's API page, such as the [Nano Banana 2 API](https://fal.ai/models/fal-ai/nano-banana-2/api).

Many image generation models on fal share a set of common arguments that behave consistently across models. These are not platform-level controls (those are [platform headers](common-parameters.md) passed via the `headers` dict). They are model inputs that affect what the model generates and how. This page documents the most common ones for image generation so you don't have to look them up on every model's API page individually.

## seed

Random seed for reproducible outputs. The same seed with the same inputs produces the same result. Useful for debugging, A/B testing, or letting users "lock" a generation they like and iterate on the prompt.

| | |
| - | - |
| **Type** | Integer or null |
| **Default** | Random |

```python Python theme={null}
result = fal_client.subscribe("fal-ai/nano-banana-2", arguments={
    "prompt": "a sunset",
    "seed": 42
})
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset", seed: 42 }
});
```

## num\_images

Number of images to generate per request. Higher values produce more results in a single call but increase processing time and cost proportionally.

| | |
| - | - |
| **Type** | Integer |
| **Default** | 1 |
| **Range** | Typically 1-4 |

```python Python theme={null}
result = fal_client.subscribe("fal-ai/nano-banana-2", arguments={
    "prompt": "a sunset",
    "num_images": 4
})
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset", num_images: 4 }
});
```

## image\_size

Output image dimensions, used by models built with fal's [toolkit](../development/working-with-files.md) (such as [FLUX](https://fal.ai/models/fal-ai/flux/schnell) models). You can pass a preset string for common aspect ratios, or a custom `{width, height}` object for precise control.

| Preset | Dimensions |
| - | - |
| `square_hd` | 1024x1024 |
| `square` | 512x512 |
| `portrait_4_3` | 768x1024 |
| `portrait_16_9` | 576x1024 |
| `landscape_4_3` | 1024x768 |
| `landscape_16_9` | 1024x576 |

```python Python theme={null}
result = fal_client.subscribe("fal-ai/flux/schnell", arguments={
    "prompt": "a sunset",
    "image_size": "landscape_16_9"
})

result = fal_client.subscribe("fal-ai/flux/schnell", arguments={
    "prompt": "a sunset",
    "image_size": {"width": 1280, "height": 720}
})
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/flux/schnell", {
  input: { prompt: "a sunset", image_size: "landscape_16_9" }
});

const result = await fal.subscribe("fal-ai/flux/schnell", {
  input: { prompt: "a sunset", image_size: { width: 1280, height: 720 } }
});
```

## aspect\_ratio

Some models (such as [Nano Banana 2](https://fal.ai/models/fal-ai/nano-banana-2)) use `aspect_ratio` instead of `image_size`. This accepts a ratio string and the model determines the output resolution internally.

| | |
| - | - |
| **Type** | String |
| **Default** | Varies by model |
| **Common values** | `"1:1"`, `"16:9"`, `"9:16"`, `"4:3"`, `"3:4"`, `"3:2"`, `"2:3"`, `"21:9"`, `"4:5"`, `"5:4"` |

```python Python theme={null}
result = fal_client.subscribe("fal-ai/nano-banana-2", arguments={
    "prompt": "a sunset",
    "aspect_ratio": "16:9"
})
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset", aspect_ratio: "16:9" }
});
```

> [!NOTE]
>
> Check the model's API page to see whether it uses `image_size` or `aspect_ratio`. Passing the wrong one will have no effect.

## user (safety identifier)

Provide a stable, privacy-preserving identifier for the end user who initiated the request. fal uses it as safety metadata to connect reported content to that end user. The identifier does not change model behavior or configure safety checks.

| | |
| - | - |
| **Type** | String |
| **Required** | No |
| **Maximum length** | 256 characters |

Use the same value for the same end user across requests. Hash or pseudonymize the identifier before you send it. Do not include names, email addresses, or other personal information. For users who are not signed in, you can use a stable session identifier.

fal includes the identifier in related safety reports. Keep a mapping in your system if you need it to investigate and take action.

```python Python theme={null}
result = fal_client.subscribe("fal-ai/nano-banana-2", arguments={
    "prompt": "a sunset",
    "user": "user_7f3a9c"
})
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset", user: "user_7f3a9c" }
});
```

## enable\_safety\_checker

Enable or disable the content safety filter on generated outputs. When enabled, fal runs an ML-based classifier on each generated image to detect NSFW content (nudity and sexual content).

| | |
| - | - |
| **Type** | Boolean |
| **Default** | `true` |
| **Also accepted as** | `enable_safety_checks` |

Each generated image is analyzed by a classifier after generation. Images flagged as unsafe are replaced with a black image of the same dimensions. The response includes a `has_nsfw_concepts` array indicating which images were flagged, so you can detect and handle filtered outputs in your code.

```json theme={null}
{
  "images": [
    {"url": "https://v3.fal.media/files/...", "width": 1024, "height": 1024},
    {"url": "https://v3.fal.media/files/...", "width": 1024, "height": 1024}
  ],
  "has_nsfw_concepts": [false, true]
}
```

In this example, the second image was flagged and replaced with a black image.

```python Python theme={null}
result = fal_client.subscribe("fal-ai/nano-banana-2", arguments={
    "prompt": "a sunset",
    "enable_safety_checker": True
})

if any(result.get("has_nsfw_concepts", [])):
    print("Some images were filtered by the safety checker")
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset", enable_safety_checker: true }
});

if (result.has_nsfw_concepts?.some(Boolean)) {
  console.log("Some images were filtered by the safety checker");
}
```

To disable the safety checker:

```python Python theme={null}
result = fal_client.subscribe("fal-ai/nano-banana-2", arguments={
    "prompt": "a sunset",
    "enable_safety_checker": False
})
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset", enable_safety_checker: false }
});
```

> [!NOTE]
>
> Not all models support disabling the safety checker. Check the model's API page (e.g., [Nano Banana 2 API](https://fal.ai/models/fal-ai/nano-banana-2/api)) for the available parameters.

## enable\_prompt\_expansion

When enabled, fal runs your prompt through an LLM before passing it to the model. The LLM rewrites short or vague prompts into richer descriptions with detail on composition, textures, colors, lighting, and mood. For example, "a sunset" might become a detailed scene description with specific lighting conditions, color palettes, and framing. This typically improves output quality for brief prompts but may alter the result if your prompt is already detailed and precise.

| | |
| - | - |
| **Type** | Boolean |
| **Default** | Varies by model |
| **Also accepted as** | `expand_prompt` (some models use this name instead) |
| **Additional cost** | Small per-request charge for the LLM inference step |

The expansion adds an LLM inference step before generation, so expect slightly higher latency per request. The expanded prompt is not returned in the response, so if you want to see what the model actually received, run the model with expansion disabled and handle prompt enhancement in your own pipeline.

```python Python theme={null}
result = fal_client.subscribe("fal-ai/nano-banana-2", arguments={
    "prompt": "a sunset",
    "expand_prompt": True
})
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: { prompt: "a sunset", expand_prompt: true }
});
```

> [!NOTE]
>
> Check the model's API page for the exact parameter name. Some models use `enable_prompt_expansion`, others use `expand_prompt`.

## output\_format

Format of the generated image file. Choose based on your use case: `jpeg` for smaller file sizes with lossy compression, `png` for lossless quality with transparency support, or `webp` for a modern format that balances both.

| | |
| - | - |
| **Type** | String |
| **Default** | Varies by model (typically `"jpeg"` or `"png"`) |
| **Values** | `"jpeg"`, `"jpg"`, `"png"`, `"webp"`, `"gif"` |

```python Python theme={null}
result = fal_client.subscribe("fal-ai/flux/schnell", arguments={
    "prompt": "a sunset",
    "output_format": "png"
})
```

```javascript JavaScript theme={null}
const result = await fal.subscribe("fal-ai/flux/schnell", {
  input: { prompt: "a sunset", output_format: "png" }
});
```
