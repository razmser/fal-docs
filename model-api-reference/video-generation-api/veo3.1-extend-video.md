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

# Veo3.1 Extend Video API

> API reference for Veo3.1 Extend Video. Extend Veo-Created Videos up to 30 seconds

**Endpoint:** `POST https://fal.run/fal-ai/veo3.1/extend-video`
**Endpoint ID:** `fal-ai/veo3.1/extend-video`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/veo3.1/extend-video/playground)
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
    "fal-ai/veo3.1/extend-video",
    arguments={
        "prompt": "Continue the scene naturally, maintaining the same style and motion.",
        "video_url": "https://v3b.fal.media/files/b/0a8670fe/pY8UGl4_C452wOm9XUBYO_9ae04df8771c4f3f979fa5cabeca6ada.mp4"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/veo3.1/extend-video", {
  input: {
      prompt: "Continue the scene naturally, maintaining the same style and motion.",
      video_url: "https://v3b.fal.media/files/b/0a8670fe/pY8UGl4_C452wOm9XUBYO_9ae04df8771c4f3f979fa5cabeca6ada.mp4"
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
  --url https://fal.run/fal-ai/veo3.1/extend-video \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "prompt": "Continue the scene naturally, maintaining the same style and motion.",
  "video_url": "https://v3b.fal.media/files/b/0a8670fe/pY8UGl4_C452wOm9XUBYO_9ae04df8771c4f3f979fa5cabeca6ada.mp4"
}'
```

## Related

* [Veo 3.1](https://fal.ai/docs/model-api-reference/video-generation-api/veo-3.1) — Video Generation
* [Veo 3.1 Fast](https://fal.ai/docs/model-api-reference/video-generation-api/veo-3.1-fast) — Video Generation
* [Veo3.1 Lite Image to Video](https://fal.ai/docs/model-api-reference/video-generation-api/veo3.1-lite-image-to-video) — Video Generation
* [Veo3.1 Lite FLF](https://fal.ai/docs/model-api-reference/video-generation-api/veo3.1-lite-flf) — Video Generation
* [Veo3.1 Lite Text to Video](https://fal.ai/docs/model-api-reference/video-generation-api/veo3.1-lite-text-to-video) — Video Generation

## Capabilities

* Text prompt input
* Aspect ratio control
* Duration control
* Negative prompts
* Reproducible generation (seed)
* Video input

## API Reference

### Input Schema

**prompt** `string` *required*

The text prompt describing how the video should be extended

**aspect\_ratio** `AspectRatioEnum` (default: auto)

The aspect ratio of the generated video. Default value: `"auto"`

Possible values: `auto`, `16:9`, `9:16`

**duration** `string` (default: 7s)

The duration of the generated video. Default value: `"7s"`

**negative\_prompt** `string`

A negative prompt to guide the video generation.

**resolution** `string` (default: 720p)

The resolution of the generated video. Default value: `"720p"`

**generate\_audio** `boolean` (default: true)

Whether to generate audio for the video. Default value: `true`

**seed** `integer`

The seed for the random number generator.

**auto\_fix** `boolean` (default: false)

Whether to automatically attempt to fix prompts that fail content policy or other validation checks by rewriting them.

**safety\_tolerance** `SafetyToleranceEnum` (default: 4)

The safety tolerance level for content moderation. 1 is the most strict (blocks most content), 6 is the least strict. Default value: `"4"`

Possible values: `1`, `2`, `3`, `4`, `5`, `6`

**video\_url** `string` *required*

URL of the video to extend. The video should be 720p or 1080p resolution in 16:9 or 9:16 aspect ratio.

### Output Schema

**video** `File` *required*

The extended video.

## Input Example

```json theme={null}
{
  "prompt": "Continue the scene naturally, maintaining the same style and motion.",
  "aspect_ratio": "auto",
  "duration": "7s",
  "resolution": "720p",
  "generate_audio": true,
  "auto_fix": false,
  "safety_tolerance": "4",
  "video_url": "https://v3b.fal.media/files/b/0a8670fe/pY8UGl4_C452wOm9XUBYO_9ae04df8771c4f3f979fa5cabeca6ada.mp4"
}
```

## Output Example

```json theme={null}
{
  "video": {
    "url": "https://v3b.fal.media/files/b/0a86711b/B_Z96VS4X9Dfd4M5ArB4H_c666e63f729f4a8fa1145c6727cef97d.mp4"
  }
}
```

## Limitations

* `aspect_ratio` restricted to: `auto`, `16:9`, `9:16`
* `safety_tolerance` restricted to: `1`, `2`, `3`, `4`, `5`, `6`
