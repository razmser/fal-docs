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

# Hunyuan 3d V3.1 Part API

> API reference for Hunyuan 3d V3.1 Part. Split 3D models into parts with Hunyuan 3D

**Endpoint:** `POST https://fal.run/fal-ai/hunyuan-3d/v3.1/part`
**Endpoint ID:** `fal-ai/hunyuan-3d/v3.1/part`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/hunyuan-3d/v3.1/part/playground)
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
    "fal-ai/hunyuan-3d/v3.1/part",
    arguments={
        "input_file_url": "https://v3b.fal.media/files/b/0a8bf92a/XECcItG5QHt0LViFTRCON_converted.fbx"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/hunyuan-3d/v3.1/part", {
  input: {
      input_file_url: "https://v3b.fal.media/files/b/0a8bf92a/XECcItG5QHt0LViFTRCON_converted.fbx"
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
  --url https://fal.run/fal-ai/hunyuan-3d/v3.1/part \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "input_file_url": "https://v3b.fal.media/files/b/0a8bf92a/XECcItG5QHt0LViFTRCON_converted.fbx"
}'
```

## Related

* [Hunyuan 3D Pro Image to 3D](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-pro-image-to-3d) — 3D
* [Hunyuan 3D Pro Text to 3D](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-pro-text-to-3d) — 3D
* [Hunyuan 3D Rapid Image to 3D](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-rapid-image-to-3d) — 3D
* [Hunyuan 3d](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d) — 3D
* [Hunyuan 3D Smart Topology](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-smart-topology) — 3D

## API Reference

### Input Schema

**input\_file\_url** `string` *required*

URL of FBX file to split into parts. ONLY FBX format supported. Max size: 100MB, face count ≤30,000. Recommended: AIGC-generated models.

### Output Schema

**result\_files** `list\<File\>` *required*

List of generated part files in FBX format

## Input Example

```json theme={null}
{
  "input_file_url": "https://v3b.fal.media/files/b/0a8bf92a/XECcItG5QHt0LViFTRCON_converted.fbx"
}
```

## Output Example

```json theme={null}
{
  "result_files": [
    {
      "content_type": "application/octet-stream",
      "file_name": "part_0.fbx",
      "file_size": 2048000,
      "url": "https://v3b.fal.media/files/b/0a8bf94b/zOP--lT23slziGKp99dJm_part_0.fbx"
    }
  ]
}
```
