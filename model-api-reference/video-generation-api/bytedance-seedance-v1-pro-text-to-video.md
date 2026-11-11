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

# Bytedance Seedance V1 Pro Text To Video API

> API reference for Bytedance Seedance V1 Pro Text To Video. Seedance 1.0 Pro, a high quality video generation model developed by Bytedance.

**Endpoint:** `POST https://fal.run/fal-ai/bytedance/seedance/v1/pro/text-to-video`
**Endpoint ID:** `fal-ai/bytedance/seedance/v1/pro/text-to-video`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/bytedance/seedance/v1/pro/text-to-video/playground)
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
    "fal-ai/bytedance/seedance/v1/pro/text-to-video",
    arguments={
        "prompt": "A bright blue race car speeds along a snowy racetrack. [Low-angle shot] Captures several cars speeding along the racetrack through a harsh snowstorm. [Overhead shot] The camera gradually pulls upward, revealing the full race scene illuminated by storm lights"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/bytedance/seedance/v1/pro/text-to-video", {
  input: {
      prompt: "A bright blue race car speeds along a snowy racetrack. [Low-angle shot] Captures several cars speeding along the racetrack through a harsh snowstorm. [Overhead shot] The camera gradually pulls upward, revealing the full race scene illuminated by storm lights"
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
  --url https://fal.run/fal-ai/bytedance/seedance/v1/pro/text-to-video \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "prompt": "A bright blue race car speeds along a snowy racetrack. [Low-angle shot] Captures several cars speeding along the racetrack through a harsh snowstorm. [Overhead shot] The camera gradually pulls upward, revealing the full race scene illuminated by storm lights"
}'
```

## Related

* [Seedance 1.0 Pro](https://fal.ai/docs/model-api-reference/video-generation-api/seedance-1.0-pro) — Video Generation
* [Seedance 1.0 Lite](https://fal.ai/docs/model-api-reference/video-generation-api/seedance-1.0-lite) — Video Generation
* [Bytedance](https://fal.ai/docs/model-api-reference/video-generation-api/bytedance) — Video Generation

## Capabilities

* Text prompt input
* Aspect ratio control
* Duration control
* Reproducible generation (seed)
* Safety checker
* Frame count control

## API Reference

### Input Schema

**prompt** `string` *required*

The text prompt used to generate the video

**aspect\_ratio** `AspectRatioEnum` (default: 16:9)

The aspect ratio of the generated video Default value: `"16:9"`

Possible values: `21:9`, `16:9`, `4:3`, `1:1`, `3:4`, `9:16`

**resolution** `ResolutionEnum` (default: 1080p)

Video resolution - 480p for faster generation, 720p for balance, 1080p for higher quality Default value: `"1080p"`

Possible values: `480p`, `720p`, `1080p`

**duration** `DurationEnum` (default: 5)

Duration of the video in seconds Default value: `"5"`

Possible values: `2`, `3`, `4`, `5`, `6`, `7`, `8`, `9`, `10`, `11`, `12`

**camera\_fixed** `boolean` (default: false)

Whether to fix the camera position

**seed** `integer`

Random seed to control video generation. Use -1 for random.

**enable\_safety\_checker** `boolean` (default: true)

If set to true, the safety checker will be enabled. Default value: `true`

**num\_frames** `integer`

The number of frames to generate. If provided, will override duration.

Range: `29` to `289`

### Output Schema

**video** `File` *required*

Generated video file

**seed** `integer` *required*

Seed used for generation

## Input Example

```json theme={null}
{
  "prompt": "A bright blue race car speeds along a snowy racetrack. [Low-angle shot] Captures several cars speeding along the racetrack through a harsh snowstorm. [Overhead shot] The camera gradually pulls upward, revealing the full race scene illuminated by storm lights",
  "aspect_ratio": "16:9",
  "resolution": "1080p",
  "duration": "5",
  "camera_fixed": false,
  "enable_safety_checker": true
}
```

## Output Example

```json theme={null}
{
  "video": {
    "url": "https://storage.googleapis.com/falserverless/example_inputs/seedance_pro_t2v.mp4"
  },
  "seed": 42
}
```

## Limitations

* `aspect_ratio` restricted to: `21:9`, `16:9`, `4:3`, `1:1`, `3:4`, `9:16`
* `resolution` restricted to: `480p`, `720p`, `1080p`
* `num_frames` range: 29 to 289
* Content moderation via safety checker
