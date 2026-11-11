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

# Kling Video V2.6 Standard API

> API reference for Kling Video V2.6 Standard. Transfer movements from a reference video to any character image. Cost-effective mode for motion transfer, perfect for portraits and simple animations.

**Endpoint:** `POST https://fal.run/fal-ai/kling-video/v2.6/standard/motion-control`
**Endpoint ID:** `fal-ai/kling-video/v2.6/standard/motion-control`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/v2.6/standard/motion-control/playground)
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
    "fal-ai/kling-video/v2.6/standard/motion-control",
    arguments={
        "image_url": "https://v3b.fal.media/files/b/0a875302/8NaxQrQxDNHppHtqcchMm.png",
        "video_url": "https://v3b.fal.media/files/b/0a8752bc/2xrNS217ngQ3wzXqA7LXr_output.mp4",
        "character_orientation": "video"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/kling-video/v2.6/standard/motion-control", {
  input: {
      image_url: "https://v3b.fal.media/files/b/0a875302/8NaxQrQxDNHppHtqcchMm.png",
      video_url: "https://v3b.fal.media/files/b/0a8752bc/2xrNS217ngQ3wzXqA7LXr_output.mp4",
      character_orientation: "video"
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
  --url https://fal.run/fal-ai/kling-video/v2.6/standard/motion-control \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "image_url": "https://v3b.fal.media/files/b/0a875302/8NaxQrQxDNHppHtqcchMm.png",
  "video_url": "https://v3b.fal.media/files/b/0a8752bc/2xrNS217ngQ3wzXqA7LXr_output.mp4",
  "character_orientation": "video"
}'
```

### Input Schema

**prompt** `string`

**image\_url** `string` *required*

Reference image URL. The characters, backgrounds, and other elements in the generated video are based on this reference image. Characters should have clear body proportions, avoid occlusion, and occupy more than 5% of the image area.

**video\_url** `string` *required*

Reference video URL. The character actions in the generated video will be consistent with this reference video. Should contain a realistic style character with entire body or upper body visible, including head, without obstruction. Duration limit depends on character\_orientation: 10s max for 'image', 30s max for 'video'.

**keep\_original\_sound** `boolean` (default: true)

Whether to keep the original sound from the reference video. Default value: `true`

**character\_orientation** `CharacterOrientationEnum` *required*

Controls whether the output character's orientation matches the reference image or video. 'video': orientation matches reference video - better for complex motions (max 30s). 'image': orientation matches reference image - better for following camera movements (max 10s).

Possible values: `image`, `video`

### Output Schema

**video** `File` *required*

The generated video

### Input Example

```json theme={null}
{
  "prompt": "An african american woman dancing",
  "image_url": "https://v3b.fal.media/files/b/0a875302/8NaxQrQxDNHppHtqcchMm.png",
  "video_url": "https://v3b.fal.media/files/b/0a8752bc/2xrNS217ngQ3wzXqA7LXr_output.mp4",
  "keep_original_sound": true,
  "character_orientation": "video"
}
```

### Output Example

```json theme={null}
{
  "video": {
    "content_type": "video/mp4",
    "file_name": "output.mp4",
    "file_size": 35299865,
    "url": "https://v3b.fal.media/files/b/0a875336/8p3rFiXtx3fE2TLoh59KP_output.mp4"
  }
}
```

## Related

* [Kling Video v2.6 Image to Video](https://fal.ai/docs/model-api-reference/video-generation-api/kling-video-v2.6-image-to-video) — Video Generation
* [Kling Video v2.6 Text to Video](https://fal.ai/docs/model-api-reference/video-generation-api/kling-video-v2.6-text-to-video) — Video Generation
* [Kling Video v2.6 Motion Control \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-video-v2.6-motion-control) — Video Generation

## Limitations

* `character_orientation` restricted to: `image`, `video`
