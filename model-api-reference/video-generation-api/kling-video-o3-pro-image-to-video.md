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

# Kling Video O3 Pro Image To Video API

> API reference for Kling Video O3 Pro Image To Video. Generate a video by taking a start frame and an end frame, animating the transition between them while following text-driven style and scene guidan

**Endpoint:** `POST https://fal.run/fal-ai/kling-video/o3/pro/image-to-video`
**Endpoint ID:** `fal-ai/kling-video/o3/pro/image-to-video`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/o3/pro/image-to-video/playground)
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
    "fal-ai/kling-video/o3/pro/image-to-video",
    arguments={
        "image_url": "https://v3b.fal.media/files/b/0a8cfd5a/8ABMp4n9rh3kfD2Rq8fHd_start_frame.png"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/kling-video/o3/pro/image-to-video", {
  input: {
      image_url: "https://v3b.fal.media/files/b/0a8cfd5a/8ABMp4n9rh3kfD2Rq8fHd_start_frame.png"
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
  --url https://fal.run/fal-ai/kling-video/o3/pro/image-to-video \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "image_url": "https://v3b.fal.media/files/b/0a8cfd5a/8ABMp4n9rh3kfD2Rq8fHd_start_frame.png"
}'
```

## Examples

> The character walks forward slowly, with the camera following from behind.

<video src="https://v3b.fal.media/files/b/0a932ef6/Qxfmb0COHKJdwlePT_7Xb_output.mp4" controls width="100%" />

## Related

* [Kling O3 Image to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-image-to-video) — Video Generation
* [Kling O3 Reference to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-reference-to-video) — Video Generation
* [Kling O3 Edit Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-edit-video) — Video Generation
* [Kling O3 Text to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-text-to-video) — Video Generation
* [Kling O3 Reference Video to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-reference-video-to-video) — Video Generation

## Capabilities

* Text prompt input
* Image input
* Duration control

## API Reference

### Input Schema

**prompt** `string`

Text prompt for video generation. Either prompt or multi\_prompt must be provided, but not both.

**image\_url** `string` *required*

URL of the start frame image.

**end\_image\_url** `string`

URL of the end frame image (optional).

**duration** `DurationEnum` (default: 5)

Video duration in seconds (3-15s). Default value: `"5"`

Possible values: `3`, `4`, `5`, `6`, `7`, `8`, `9`, `10`, `11`, `12`, `13`, `14`, `15`

**generate\_audio** `boolean` (default: false)

Whether to generate native audio for the video.

**multi\_prompt** `list\<KlingV3MultiPromptElement\>`

List of prompts for multi-shot video generation.

**shot\_type** `string` (default: customize)

The type of multi-shot video generation. Default value: `"customize"`

### Output Schema

**video** `File` *required*

The generated video.

## Input Example

```json theme={null}
{
  "prompt": "The character walks forward slowly, with the camera following from behind.",
  "image_url": "https://v3b.fal.media/files/b/0a8cfd5a/8ABMp4n9rh3kfD2Rq8fHd_start_frame.png",
  "end_image_url": "https://v3b.fal.media/files/b/0a8d0248/S415GcxackjjLKom6f3jq_VTImWrNO.png",
  "duration": "10",
  "generate_audio": false,
  "multi_prompt": null,
  "shot_type": "customize"
}
```

## Output Example

```json theme={null}
{
  "video": {
    "content_type": "video/mp4",
    "file_name": "output.mp4",
    "file_size": 12037975,
    "url": "https://v3b.fal.media/files/b/0a8d0278/pgIO9yOXTFCTetKBsqDwX_output.mp4"
  }
}
```
