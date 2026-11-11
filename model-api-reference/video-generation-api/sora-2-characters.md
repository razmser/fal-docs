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

# Sora 2 Characters API

> API reference for Sora 2 Characters. Generate character ids to use with Sora 2 generations

**Endpoint:** `POST https://fal.run/fal-ai/sora-2/characters`
**Endpoint ID:** `fal-ai/sora-2/characters`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/sora-2/characters/playground)
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
    "fal-ai/sora-2/characters",
    arguments={
        "video_url": "",
        "name": ""
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/sora-2/characters", {
  input: {
      video_url: "",
      name: ""
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
  --url https://fal.run/fal-ai/sora-2/characters \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "video_url": "",
  "name": ""
}'
```

## Related

* [Sora 2](https://fal.ai/docs/model-api-reference/video-generation-api/sora-2) — Video Generation

## Capabilities

* Video input

## API Reference

### Input Schema

**video\_url** `string` *required*

URL of an MP4 video (minimum 720p, max \~2.67:1 aspect ratio) to define the character. Videos exceeding 1080p are automatically scaled down. Non-standard aspect ratios are automatically padded to 16:9 (landscape) or 9:16 (portrait). Videos longer than 4 seconds are trimmed to the first 4 seconds.

**name** `string` *required*

Name for the character (1–80 characters). Refer to this name in prompts when using the character.

### Output Schema

**id** `string` *required*

API character ID (format char\_...). Use this in character\_ids when generating video.

**name** `string` *required*

The character name

## Input Example

```json theme={null}
{
  "video_url": "",
  "name": ""
}
```

## Output Example

```json theme={null}
{
  "id": "char_123",
  "name": ""
}
```
