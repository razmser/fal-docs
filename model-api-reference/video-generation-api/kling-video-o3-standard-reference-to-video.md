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

# Kling Video O3 Standard Reference To Video API

> API reference for Kling Video O3 Standard Reference To Video. Transform images, elements, and text into consistent, high-quality video scenes, ensuring stable character identity, object details, and e

**Endpoint:** `POST https://fal.run/fal-ai/kling-video/o3/standard/reference-to-video`
**Endpoint ID:** `fal-ai/kling-video/o3/standard/reference-to-video`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/o3/standard/reference-to-video/playground)
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
    "fal-ai/kling-video/o3/standard/reference-to-video",
    arguments={},
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/kling-video/o3/standard/reference-to-video", {
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
  --url https://fal.run/fal-ai/kling-video/o3/standard/reference-to-video \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{}'
```

## Related

* [Kling O3 Image to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-image-to-video) — Video Generation
* [Kling O3 Reference to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-reference-to-video) — Video Generation
* [Kling O3 Edit Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-edit-video) — Video Generation
* [Kling O3 Text to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-text-to-video) — Video Generation
* [Kling O3 Reference Video to Video \[Pro\]](https://fal.ai/docs/model-api-reference/video-generation-api/kling-o3-reference-video-to-video) — Video Generation

## Capabilities

* Text prompt input
* Duration control
* Aspect ratio control

## API Reference

### Input Schema

**prompt** `string`

Text prompt for video generation. Either prompt or multi\_prompt must be provided, but not both.

**multi\_prompt** `list\<KlingV3MultiPromptElement\>`

List of prompts for multi-shot video generation.

**start\_image\_url** `string`

Image to use as the first frame of the video.

**end\_image\_url** `string`

Image to use as the last frame of the video.

**image\_urls** `list\<string\>`

Reference images for style/appearance. Reference in prompt as @Image1, @Image2, etc. Maximum 4 total (elements + reference images) when using video.

**elements** `list\<KlingV3ComboElementInput\>`

Elements (characters/objects) to include. Reference in prompt as @Element1, @Element2.

**generate\_audio** `boolean` (default: false)

Whether to generate native audio for the video.

**duration** `DurationEnum` (default: 5)

Video duration in seconds (3-15s). Default value: `"5"`

Possible values: `3`, `4`, `5`, `6`, `7`, `8`, `9`, `10`, `11`, `12`, `13`, `14`, `15`

**shot\_type** `string` (default: customize)

The type of multi-shot video generation. Default value: `"customize"`

**aspect\_ratio** `AspectRatioEnum` (default: 16:9)

The aspect ratio of the generated video frame. Default value: `"16:9"`

Possible values: `16:9`, `9:16`, `1:1`

### Output Schema

**video** `File` *required*

The generated video.

## Input Example

```json theme={null}
{
  "prompt": "@Element1 and @Element2 is having dinner at this table on @Image1",
  "multi_prompt": null,
  "start_image_url": "https://v3b.fal.media/files/b/0a8d1b63/EcYdmuNB1LTFjtn3Ryjrf_6ROoQV4u.png",
  "image_urls": [
    "https://v3b.fal.media/files/b/0a8d1b63/EcYdmuNB1LTFjtn3Ryjrf_6ROoQV4u.png"
  ],
  "elements": [
    {
      "frontal_image_url": "https://v3b.fal.media/files/b/0a8d1b64/-ddhNV-utVpHj_1uGfuY-_i6viYke3.png",
      "reference_image_urls": [
        "https://v3b.fal.media/files/b/0a8d1b5e/Gl1qUHJeTG63vAGtQmGM-_S3kPW32v.png"
      ]
    },
    {
      "frontal_image_url": "https://v3b.fal.media/files/b/0a8d1b66/cWe8LC84I_OF6ee9ZtnsO_nY9Hw5UB.png",
      "reference_image_urls": [
        "https://v3b.fal.media/files/b/0a8d1b60/Dt21s8LElZdSccIGDC7ec_Nuaraa9P.png"
      ]
    }
  ],
  "generate_audio": false,
  "duration": "8",
  "shot_type": "customize",
  "aspect_ratio": "16:9"
}
```

## Output Example

```json theme={null}
{
  "video": {
    "content_type": "video/mp4",
    "file_name": "output.mp4",
    "file_size": 3192162,
    "url": "https://v3b.fal.media/files/b/0a8d200d/ejCxI5DalzOPlP4yf6uP3_output.mp4"
  }
}
```

## Limitations

* `aspect_ratio` restricted to: `16:9`, `9:16`, `1:1`
