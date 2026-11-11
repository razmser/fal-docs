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

# Bytedance Video Stylize API

> API reference for Bytedance Video Stylize. Transform your images into stylized videos using this workflow.

**Endpoint:** `POST https://fal.run/fal-ai/bytedance/video-stylize`
**Endpoint ID:** `fal-ai/bytedance/video-stylize`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/bytedance/video-stylize/playground)
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
    "fal-ai/bytedance/video-stylize",
    arguments={
        "style": "Manga style",
        "image_url": "https://v3.fal.media/files/kangaroo/-KmSPIcXeGA3Z_iiH4C75_tmph2ry_0_8.png"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/bytedance/video-stylize", {
  input: {
      style: "Manga style",
      image_url: "https://v3.fal.media/files/kangaroo/-KmSPIcXeGA3Z_iiH4C75_tmph2ry_0_8.png"
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
  --url https://fal.run/fal-ai/bytedance/video-stylize \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "style": "Manga style",
  "image_url": "https://v3.fal.media/files/kangaroo/-KmSPIcXeGA3Z_iiH4C75_tmph2ry_0_8.png"
}'
```

## Capabilities

* Image input

## API Reference

### Input Schema

**style** `string` *required*

The style for your character in the video. Please use a short description.

**image\_url** `string` *required*

URL of the image to make the stylized video from.

## Input Example

```json theme={null}
{
  "style": "Manga style",
  "image_url": "https://v3.fal.media/files/kangaroo/-KmSPIcXeGA3Z_iiH4C75_tmph2ry_0_8.png"
}
```
