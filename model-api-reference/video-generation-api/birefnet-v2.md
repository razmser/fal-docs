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

# Birefnet V2 API

> API reference for Birefnet V2. Video background removal version of bilateral reference framework (BiRefNet) for high-resolution dichotomous image segmentation (DIS)

**Endpoint:** `POST https://fal.run/fal-ai/birefnet/v2/video`
**Endpoint ID:** `fal-ai/birefnet/v2/video`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/birefnet/v2/video/playground)
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
    "fal-ai/birefnet/v2/video",
    arguments={
        "video_url": "https://storage.googleapis.com/falserverless/example_inputs/birefnet-video-input.mp4"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/birefnet/v2/video", {
  input: {
      video_url: "https://storage.googleapis.com/falserverless/example_inputs/birefnet-video-input.mp4"
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
  --url https://fal.run/fal-ai/birefnet/v2/video \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "video_url": "https://storage.googleapis.com/falserverless/example_inputs/birefnet-video-input.mp4"
}'
```

### Input Schema

**model** `ModelEnum` (default: General Use (Light))

Model to use for background removal.
The 'General Use (Light)' model is the original model used in the BiRefNet repository.
The 'General Use (Light 2K)' model is the original model used in the BiRefNet repository but trained with 2K images.
The 'General Use (Heavy)' model is a slower but more accurate model.
The 'Matting' model is a model trained specifically for matting images.
The 'Portrait' model is a model trained specifically for portrait images.
The 'General Use (Dynamic)' model supports dynamic resolutions from 256x256 to 2304x2304.
The 'General Use (Light)' model is recommended for most use cases.

The corresponding models are as follows:

* 'General Use (Light)': BiRefNet
* 'General Use (Light 2K)': BiRefNet\_lite-2K
* 'General Use (Heavy)': BiRefNet\_lite
* 'Matting': BiRefNet-matting
* 'Portrait': BiRefNet-portrait
* 'General Use (Dynamic)': BiRefNet\_dynamic Default value: `"General Use (Light)"`

Possible values: `General Use (Light)`, `General Use (Light 2K)`, `General Use (Heavy)`, `Matting`, `Portrait`, `General Use (Dynamic)`

**operating\_resolution** `OperatingResolutionEnum` (default: 1024x1024)

The resolution to operate on. The higher the resolution, the more accurate the output will be for high res input images. The '2304x2304' option is only available for the 'General Use (Dynamic)' model. Default value: `"1024x1024"`

Possible values: `1024x1024`, `2048x2048`, `2304x2304`

**output\_mask** `boolean` (default: false)

Whether to output the mask used to remove the background

**refine\_foreground** `boolean` (default: true)

Whether to refine the foreground using the estimated mask Default value: `true`

**sync\_mode** `boolean` (default: false)

If `True`, the media will be returned as a data URI and the output data won't be available in the request history.

**video\_url** `string` *required*

URL of the video to remove background from

**video\_output\_type** `VideoOutputTypeEnum` (default: X264 (.mp4))

The output type of the generated video. Default value: `"X264 (.mp4)"`

Possible values: `X264 (.mp4)`, `VP9 (.webm)`, `PRORES4444 (.mov)`, `GIF (.gif)`

**video\_quality** `VideoQualityEnum` (default: high)

The quality of the generated video. Default value: `"high"`

Possible values: `low`, `medium`, `high`, `maximum`

**video\_write\_mode** `VideoWriteModeEnum` (default: balanced)

The write mode of the generated video. Default value: `"balanced"`

Possible values: `fast`, `balanced`, `small`

### Output Schema

**video** `VideoFile` *required*

Video with background removed

**mask\_video** `VideoFile`

Mask used to remove the background

### Input Example

```json theme={null}
{
  "model": "General Use (Light)",
  "operating_resolution": "1024x1024",
  "output_mask": false,
  "refine_foreground": true,
  "sync_mode": false,
  "video_url": "https://storage.googleapis.com/falserverless/example_inputs/birefnet-video-input.mp4",
  "video_output_type": "X264 (.mp4)",
  "video_quality": "high",
  "video_write_mode": "balanced"
}
```

### Output Example

```json theme={null}
{
  "video": {
    "content_type": "video/webm",
    "duration": 8,
    "file_name": "birefnet-video-output.webm",
    "fps": 24,
    "height": 1080,
    "num_frames": 192,
    "url": "https://storage.googleapis.com/falserverless/example_outputs/birefnet-video-output.webm",
    "width": 1920
  }
}
```

## Limitations

* `model` restricted to: `General Use (Light)`, `General Use (Light 2K)`, `General Use (Heavy)`, `Matting`, `Portrait`, `General Use (Dynamic)`
* `operating_resolution` restricted to: `1024x1024`, `2048x2048`, `2304x2304`
* `video_output_type` restricted to: `X264 (.mp4)`, `VP9 (.webm)`, `PRORES4444 (.mov)`, `GIF (.gif)`
* `video_quality` restricted to: `low`, `medium`, `high`, `maximum`
* `video_write_mode` restricted to: `fast`, `balanced`, `small`
