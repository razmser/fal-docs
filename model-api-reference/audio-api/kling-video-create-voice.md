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

# Kling Video Create Voice API

> API reference for Kling Video Create Voice. Create Voices to be used with Kling Models Voice Control

**Endpoint:** `POST https://fal.run/fal-ai/kling-video/create-voice`
**Endpoint ID:** `fal-ai/kling-video/create-voice`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/create-voice/playground)
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
    "fal-ai/kling-video/create-voice",
    arguments={
        "voice_url": "https://v3b.fal.media/files/b/0a867736/_Wo19V-XrOVYZt6jKE8t5_kling_video.wav"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/kling-video/create-voice", {
  input: {
      voice_url: "https://v3b.fal.media/files/b/0a867736/_Wo19V-XrOVYZt6jKE8t5_kling_video.wav"
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
  --url https://fal.run/fal-ai/kling-video/create-voice \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "voice_url": "https://v3b.fal.media/files/b/0a867736/_Wo19V-XrOVYZt6jKE8t5_kling_video.wav"
}'
```

## API Reference

### Input Schema

**voice\_url** `string` *required*

URL of the voice audio file. Supports .mp3/.wav audio or .mp4/.mov video. Duration must be 5-30 seconds with clean, single-voice audio.

### Output Schema

**voice\_id** `string` *required*

Unique identifier for the created voice

## Input Example

```json theme={null}
{
  "voice_url": "https://v3b.fal.media/files/b/0a867736/_Wo19V-XrOVYZt6jKE8t5_kling_video.wav"
}
```

## Output Example

```json theme={null}
{
  "voice_id": "829877809978941442"
}
```
