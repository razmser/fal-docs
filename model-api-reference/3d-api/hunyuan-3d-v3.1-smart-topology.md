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

# Hunyuan 3d V3.1 Smart Topology API

> API reference for Hunyuan 3d V3.1 Smart Topology. Optimize 3D mesh topology with Hunyuan 3D Smart Topology.

**Endpoint:** `POST https://fal.run/fal-ai/hunyuan-3d/v3.1/smart-topology`
**Endpoint ID:** `fal-ai/hunyuan-3d/v3.1/smart-topology`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/hunyuan-3d/v3.1/smart-topology/playground)
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
    "fal-ai/hunyuan-3d/v3.1/smart-topology",
    arguments={},
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/hunyuan-3d/v3.1/smart-topology", {
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
  --url https://fal.run/fal-ai/hunyuan-3d/v3.1/smart-topology \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{}'
```

## Related

* [Hunyuan 3D Pro Image to 3D](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-pro-image-to-3d) — 3D
* [Hunyuan 3D Pro Text to 3D](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-pro-text-to-3d) — 3D
* [Hunyuan 3D Rapid Image to 3D](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-rapid-image-to-3d) — 3D
* [Hunyuan 3d](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d) — 3D
* [Hunyuan 3D Part Splitter](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-part-splitter) — 3D

## API Reference

### Input Schema

**input\_file\_url** `string` (default: [https://v3b.fal.media/files/b/0a8c09c0/VYDiCTcDGK55qY2-idGbX\_model.glb](https://v3b.fal.media/files/b/0a8c09c0/VYDiCTcDGK55qY2-idGbX_model.glb))

URL of GLB or OBJ file to optimize topology. Max size: 200MB. Default value: `"https://v3b.fal.media/files/b/0a8c09c0/VYDiCTcDGK55qY2-idGbX_model.glb"`

**input\_file\_type** `InputFileTypeEnum` (default: glb)

Input 3D file format. Default value: `"glb"`

Possible values: `glb`, `obj`

**polygon\_type** `PolygonTypeEnum` (default: triangle)

Output polygon type. triangle: triangular faces only. quadrilateral: mixed quad and triangle faces. Default value: `"triangle"`

Possible values: `triangle`, `quadrilateral`

**face\_level** `FaceLevelEnum` (default: medium)

Target polygon density. high: more detail/polygons, medium: balanced, low: fewer polygons. Default value: `"medium"`

Possible values: `high`, `medium`, `low`

### Output Schema

**model\_glb** `File` *required*

Processed 3D model with optimized topology (primary file).

**model\_urls** `ModelUrls` *required*

URLs for different 3D model formats

## Input Example

```json theme={null}
{
  "input_file_url": "https://v3b.fal.media/files/b/0a8c09c0/VYDiCTcDGK55qY2-idGbX_model.glb",
  "input_file_type": "glb",
  "polygon_type": "triangle",
  "face_level": "medium"
}
```

## Output Example

```json theme={null}
{
  "model_glb": {
    "content_type": "model/obj",
    "file_name": "model.obj",
    "file_size": 394409,
    "url": "https://v3b.fal.media/files/b/0a8c0ab4/tqMY5NJLnHjpwN8rQ15dj_model.obj"
  },
  "model_urls": {
    "glb": {
      "content_type": "model/gltf-binary",
      "file_name": "model.glb",
      "file_size": 206004,
      "url": "https://v3b.fal.media/files/b/0a8c0ab4/eX-_x0Wv8fZL05l9CGp6Y_model.glb"
    },
    "obj": {
      "content_type": "model/obj",
      "file_name": "model.obj",
      "file_size": 394409,
      "url": "https://v3b.fal.media/files/b/0a8c0ab4/tqMY5NJLnHjpwN8rQ15dj_model.obj"
    }
  }
}
```

## Limitations

* `input_file_type` restricted to: `glb`, `obj`
* `polygon_type` restricted to: `triangle`, `quadrilateral`
* `face_level` restricted to: `high`, `medium`, `low`
