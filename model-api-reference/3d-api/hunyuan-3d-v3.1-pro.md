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

# Hunyuan 3d V3.1 Pro API

> API reference for Hunyuan 3d V3.1 Pro. Generate 3D models from images with Hunyuan 3D Pro

> [tab] Image To 3d
>
> **Endpoint:** `POST https://fal.run/fal-ai/hunyuan-3d/v3.1/pro/image-to-3d`
> **Endpoint ID:** `fal-ai/hunyuan-3d/v3.1/pro/image-to-3d`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/hunyuan-3d/v3.1/pro/image-to-3d/playground)
> >
> > Run this model interactively with your own prompts.
>
> ### Quick Start
>
> ```python title="Python" theme={null}
> import fal_client
>
> def on_queue_update(update):
>     if isinstance(update, fal_client.InProgress):
>         for log in update.logs:
>            print(log["message"])
>
> result = fal_client.subscribe(
>     "fal-ai/hunyuan-3d/v3.1/pro/image-to-3d",
>     arguments={
>         "input_image_url": "https://v3b.fal.media/files/b/0a8c3155/BTXNRrzOFsO6OvdSxdXmv_6ZcaGmrY.png"
>     },
>     with_logs=True,
>     on_queue_update=on_queue_update,
> )
> print(result)
> ```
>
> ```javascript title="JavaScript" theme={null}
> import { fal } from "@fal-ai/client";
>
> const result = await fal.subscribe("fal-ai/hunyuan-3d/v3.1/pro/image-to-3d", {
>   input: {
>       input_image_url: "https://v3b.fal.media/files/b/0a8c3155/BTXNRrzOFsO6OvdSxdXmv_6ZcaGmrY.png"
>     },
>   logs: true,
>   onQueueUpdate: (update) => {
>     if (update.status === "IN_PROGRESS") {
>       update.logs.map((log) => log.message).forEach(console.log);
>     }
>   },
> });
> console.log(result.data);
> console.log(result.requestId);
> ```
>
> ```bash title="cURL" theme={null}
> curl --request POST \
>   --url https://fal.run/fal-ai/hunyuan-3d/v3.1/pro/image-to-3d \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "input_image_url": "https://v3b.fal.media/files/b/0a8c3155/BTXNRrzOFsO6OvdSxdXmv_6ZcaGmrY.png"
> }'
> ```
>
> ### Input Schema
>
> **input\_image\_url** `string` *required*
>
> Front view image URL. Resolution: 128-5000px, max 8MB, formats: JPG/PNG/WEBP. Tips: simple background, single object, object >50% of frame.
>
> **back\_image\_url** `string`
>
> Optional back/rear view image URL (JPG/PNG recommended).
>
> **left\_image\_url** `string`
>
> Optional left side view image URL (JPG/PNG recommended).
>
> **right\_image\_url** `string`
>
> Optional right side view image URL (JPG/PNG recommended).
>
> **top\_image\_url** `string`
>
> Optional top view image URL (v3.1 exclusive, JPG/PNG recommended).
>
> **bottom\_image\_url** `string`
>
> Optional bottom view image URL (v3.1 exclusive, JPG/PNG recommended).
>
> **left\_front\_image\_url** `string`
>
> Optional left-front 45 degree angle view image URL (v3.1 exclusive, JPG/PNG recommended).
>
> **right\_front\_image\_url** `string`
>
> Optional right-front 45 degree angle view image URL (v3.1 exclusive, JPG/PNG recommended).
>
> **generate\_type** `GenerateTypeEnum` (default: Normal)
>
> Generation task type. Normal: textured model. Geometry: geometry-only white model (no textures). LowPoly/Sketch are not available in v3.1. Default value: `"Normal"`
>
> Possible values: `Normal`, `Geometry`
>
> **enable\_pbr** `boolean` (default: false)
>
> Enable PBR material generation (metallic, roughness, normal textures). Ignored when generate\_type is Geometry.
>
> **face\_count** `integer` (default: 500000)
>
> Target polygon face count. Range: 40,000-1,500,000. Default: 500,000. Default value: `500000`
>
> Range: `40000` to `1500000`
>
> ### Output Schema
>
> **model\_glb** `File` *required*
>
> Generated 3D object in GLB format.
>
> **thumbnail** `File`
>
> Preview thumbnail of the generated model
>
> **model\_urls** `ModelUrls` *required*
>
> URLs for different 3D model formats
>
> **seed** `integer`
>
> The seed used for generation
>
> ### Input Example
>
> ```json theme={null}
> {
>   "input_image_url": "https://v3b.fal.media/files/b/0a8c3155/BTXNRrzOFsO6OvdSxdXmv_6ZcaGmrY.png",
>   "generate_type": "Normal",
>   "enable_pbr": false,
>   "face_count": 500000
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "model_glb": {
>     "content_type": "model/gltf-binary",
>     "file_name": "model.glb",
>     "file_size": 38554640,
>     "url": "https://v3b.fal.media/files/b/0a8c3187/jOeZmtBuhdQMkDu65AkdT_model.glb"
>   },
>   "thumbnail": {
>     "content_type": "image/png",
>     "file_name": "preview.png",
>     "file_size": 194908,
>     "url": "https://v3b.fal.media/files/b/0a8c3187/gxidaODj4OvPXruCfrZ-__preview.png"
>   },
>   "model_urls": {
>     "glb": {
>       "content_type": "model/gltf-binary",
>       "file_name": "model.glb",
>       "file_size": 38554640,
>       "url": "https://v3b.fal.media/files/b/0a8c3187/jOeZmtBuhdQMkDu65AkdT_model.glb"
>     },
>     "obj": {
>       "content_type": "model/obj",
>       "file_name": "model.obj",
>       "file_size": 31447160,
>       "url": "https://v3b.fal.media/files/b/0a8c3186/no-aBFEDnOuthILfv-wzs_model.obj"
>     }
>   }
> }
> ```

> [tab] Text To 3d
>
> **Endpoint:** `POST https://fal.run/fal-ai/hunyuan-3d/v3.1/pro/text-to-3d`
> **Endpoint ID:** `fal-ai/hunyuan-3d/v3.1/pro/text-to-3d`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/hunyuan-3d/v3.1/pro/text-to-3d/playground)
> >
> > Run this model interactively with your own prompts.
>
> ### Quick Start
>
> ```python title="Python" theme={null}
> import fal_client
>
> def on_queue_update(update):
>     if isinstance(update, fal_client.InProgress):
>         for log in update.logs:
>            print(log["message"])
>
> result = fal_client.subscribe(
>     "fal-ai/hunyuan-3d/v3.1/pro/text-to-3d",
>     arguments={
>         "prompt": "A super cool space ship with details"
>     },
>     with_logs=True,
>     on_queue_update=on_queue_update,
> )
> print(result)
> ```
>
> ```javascript title="JavaScript" theme={null}
> import { fal } from "@fal-ai/client";
>
> const result = await fal.subscribe("fal-ai/hunyuan-3d/v3.1/pro/text-to-3d", {
>   input: {
>       prompt: "A super cool space ship with details"
>     },
>   logs: true,
>   onQueueUpdate: (update) => {
>     if (update.status === "IN_PROGRESS") {
>       update.logs.map((log) => log.message).forEach(console.log);
>     }
>   },
> });
> console.log(result.data);
> console.log(result.requestId);
> ```
>
> ```bash title="cURL" theme={null}
> curl --request POST \
>   --url https://fal.run/fal-ai/hunyuan-3d/v3.1/pro/text-to-3d \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A super cool space ship with details"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> Text description of the 3D content to generate. Max 1024 UTF-8 characters.
>
> **generate\_type** `GenerateTypeEnum` (default: Normal)
>
> Generation task type. Normal: textured model. Geometry: geometry-only white model (no textures). LowPoly/Sketch are not available in v3.1. Default value: `"Normal"`
>
> Possible values: `Normal`, `Geometry`
>
> **enable\_pbr** `boolean` (default: false)
>
> Enable PBR material generation (metallic, roughness, normal textures). Ignored when generate\_type is Geometry.
>
> **face\_count** `integer` (default: 500000)
>
> Target polygon face count. Range: 40,000-1,500,000. Default: 500,000. Default value: `500000`
>
> Range: `40000` to `1500000`
>
> ### Output Schema
>
> **model\_glb** `File` *required*
>
> Generated 3D object in GLB format.
>
> **thumbnail** `File`
>
> Preview thumbnail of the generated model
>
> **model\_urls** `ModelUrls` *required*
>
> URLs for different 3D model formats
>
> **seed** `integer`
>
> The seed used for generation
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "A super cool space ship with details",
>   "generate_type": "Normal",
>   "enable_pbr": false,
>   "face_count": 500000
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "model_glb": {
>     "content_type": "model/gltf-binary",
>     "file_name": "model.glb",
>     "file_size": 35833072,
>     "url": "https://v3b.fal.media/files/b/0a8c5483/z6sbpr5wBRjqgnQlJM2Ot_model.glb"
>   },
>   "thumbnail": {
>     "content_type": "image/png",
>     "file_name": "preview.png",
>     "file_size": 68927,
>     "url": "https://v3b.fal.media/files/b/0a8c5483/EE97ZV7cM-3UVG4peyWTQ_preview.png"
>   },
>   "model_urls": {
>     "glb": {
>       "content_type": "model/gltf-binary",
>       "file_name": "model.glb",
>       "file_size": 35833072,
>       "url": "https://v3b.fal.media/files/b/0a8c5483/z6sbpr5wBRjqgnQlJM2Ot_model.glb"
>     },
>     "mtl": {
>       "content_type": "text/plain",
>       "file_name": "material.mtl",
>       "file_size": 88,
>       "url": "https://v3b.fal.media/files/b/0a8c5482/ZxJepsEkhM67VSugmZ7QT_material.mtl"
>     },
>     "obj": {
>       "content_type": "model/obj",
>       "file_name": "6168030a8817075aaa55c94cc5145000.obj",
>       "file_size": 34755929,
>       "url": "https://v3b.fal.media/files/b/0a8c5482/ZzC1xlOftyGQxhDkbZzVW_6168030a8817075aaa55c94cc5145000.obj"
>     },
>     "texture": {
>       "content_type": "image/png",
>       "file_name": "texture_20250901.png",
>       "file_size": 19996580,
>       "url": "https://v3b.fal.media/files/b/0a8c5482/jOL2nBpcOGspwxQf0_U-z_texture_20250901.png"
>     }
>   }
> }
> ```

## Related

* [Hunyuan 3D Pro Text to 3D](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-pro-text-to-3d) — 3D
* [Hunyuan 3D Rapid Image to 3D](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-rapid-image-to-3d) — 3D
* [Hunyuan 3d](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d) — 3D
* [Hunyuan 3D Smart Topology](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-smart-topology) — 3D
* [Hunyuan 3D Part Splitter](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-part-splitter) — 3D
* [Hunyuan 3D Pro Image to 3D](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-pro-image-to-3d) — 3D

## Limitations

* `generate_type` restricted to: `Normal`, `Geometry`
* `face_count` range: 40000 to 1500000
