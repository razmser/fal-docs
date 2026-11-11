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

# Hunyuan 3d V3.1 Rapid API

> API reference for Hunyuan 3d V3.1 Rapid. Rapidly generate 3D models from images using Hunyuan 3D.

> [tab] Image To 3d
>
> **Endpoint:** `POST https://fal.run/fal-ai/hunyuan-3d/v3.1/rapid/image-to-3d`
> **Endpoint ID:** `fal-ai/hunyuan-3d/v3.1/rapid/image-to-3d`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/hunyuan-3d/v3.1/rapid/image-to-3d/playground)
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
>     "fal-ai/hunyuan-3d/v3.1/rapid/image-to-3d",
>     arguments={
>         "input_image_url": "https://v3b.fal.media/files/b/0a865ab1/omYcawLUo4RZbO8J6ZgZR.png"
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
> const result = await fal.subscribe("fal-ai/hunyuan-3d/v3.1/rapid/image-to-3d", {
>   input: {
>       input_image_url: "https://v3b.fal.media/files/b/0a865ab1/omYcawLUo4RZbO8J6ZgZR.png"
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
>   --url https://fal.run/fal-ai/hunyuan-3d/v3.1/rapid/image-to-3d \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "input_image_url": "https://v3b.fal.media/files/b/0a865ab1/omYcawLUo4RZbO8J6ZgZR.png"
> }'
> ```
>
> ### Input Schema
>
> **input\_image\_url** `string` *required*
>
> Front view image URL. Resolution: 128-5000px, max 8MB (recommended ≤6MB for base64 encoding), formats: JPG/PNG/WEBP. Tips: simple background, single object, object >50% of frame.
>
> **enable\_pbr** `boolean` (default: false)
>
> Enable PBR material generation (metallic, roughness, normal textures). Does not take effect when enable\_geometry is True.
>
> **enable\_geometry** `boolean` (default: false)
>
> Generate geometry-only white model without textures. When enabled, enable\_pbr is ignored and OBJ is not supported (default output is GLB).
>
> ### Output Schema
>
> **model\_glb** `File`
>
> Generated 3D model file. Contains GLB if available, otherwise OBJ.
>
> **material\_mtl** `File`
>
> MTL material file for the OBJ model.
>
> **texture** `File`
>
> Texture image for the 3D model.
>
> **thumbnail** `File`
>
> Preview thumbnail of the generated model
>
> **model\_urls** `ModelUrls` *required*
>
> URLs for different 3D model formats.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "input_image_url": "https://v3b.fal.media/files/b/0a865ab1/omYcawLUo4RZbO8J6ZgZR.png",
>   "enable_pbr": false,
>   "enable_geometry": false
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "model_glb": {
>     "content_type": "model/obj",
>     "file_name": "8b1dbea208d194b9089a950abc2df426.obj",
>     "file_size": 3172659,
>     "url": "https://v3b.fal.media/files/b/0a8c4439/vj933H8B4W3wbd3e2RNby_8b1dbea208d194b9089a950abc2df426.obj"
>   },
>   "material_mtl": {
>     "content_type": "text/plain",
>     "file_name": "material.mtl",
>     "file_size": 88,
>     "url": "https://v3b.fal.media/files/b/0a8c4439/_RhytNH4xZ5EFHr34YzJt_material.mtl"
>   },
>   "texture": {
>     "content_type": "image/png",
>     "file_name": "texture_20250901.png",
>     "file_size": 11728567,
>     "url": "https://v3b.fal.media/files/b/0a8c4439/_4NXiSoGcZ-GYwmgUTfHZ_texture_20250901.png"
>   },
>   "thumbnail": {
>     "content_type": "image/png",
>     "file_name": "preview.png",
>     "file_size": 82521,
>     "url": "https://v3b.fal.media/files/b/0a8c4439/70Sm1pZ16SQP-mEbaKICC_preview.png"
>   },
>   "model_urls": {
>     "mtl": {
>       "content_type": "text/plain",
>       "file_name": "material.mtl",
>       "file_size": 88,
>       "url": "https://v3b.fal.media/files/b/0a8c4439/_RhytNH4xZ5EFHr34YzJt_material.mtl"
>     },
>     "obj": {
>       "content_type": "model/obj",
>       "file_name": "8b1dbea208d194b9089a950abc2df426.obj",
>       "file_size": 3172659,
>       "url": "https://v3b.fal.media/files/b/0a8c4439/vj933H8B4W3wbd3e2RNby_8b1dbea208d194b9089a950abc2df426.obj"
>     },
>     "texture": {
>       "content_type": "image/png",
>       "file_name": "texture_20250901.png",
>       "file_size": 11728567,
>       "url": "https://v3b.fal.media/files/b/0a8c4439/_4NXiSoGcZ-GYwmgUTfHZ_texture_20250901.png"
>     }
>   }
> }
> ```

> [tab] Text To 3d
>
> **Endpoint:** `POST https://fal.run/fal-ai/hunyuan-3d/v3.1/rapid/text-to-3d`
> **Endpoint ID:** `fal-ai/hunyuan-3d/v3.1/rapid/text-to-3d`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/hunyuan-3d/v3.1/rapid/text-to-3d/playground)
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
>     "fal-ai/hunyuan-3d/v3.1/rapid/text-to-3d",
>     arguments={
>         "prompt": "A rustic wooden treasure chest with metal bands and ornate lock"
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
> const result = await fal.subscribe("fal-ai/hunyuan-3d/v3.1/rapid/text-to-3d", {
>   input: {
>       prompt: "A rustic wooden treasure chest with metal bands and ornate lock"
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
>   --url https://fal.run/fal-ai/hunyuan-3d/v3.1/rapid/text-to-3d \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A rustic wooden treasure chest with metal bands and ornate lock"
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> Text description of the 3D content to generate. Max 200 UTF-8 characters.
>
> **enable\_pbr** `boolean` (default: false)
>
> Enable PBR material generation (metallic, roughness, normal textures). Does not take effect when enable\_geometry is True.
>
> **enable\_geometry** `boolean` (default: false)
>
> Generate geometry-only white model without textures. When enabled, enable\_pbr is ignored and OBJ is not supported (default output is GLB).
>
> ### Output Schema
>
> **model\_obj** `File`
>
> Generated 3D model in OBJ format.
>
> **material\_mtl** `File`
>
> MTL material file for the OBJ model.
>
> **texture** `File`
>
> Texture image for the 3D model.
>
> **thumbnail** `File`
>
> Preview thumbnail of the generated model
>
> **model\_urls** `ModelUrls` *required*
>
> URLs for different 3D model formats.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "A rustic wooden treasure chest with metal bands and ornate lock",
>   "enable_pbr": false,
>   "enable_geometry": false
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "model_obj": {
>     "content_type": "model/obj",
>     "file_name": "0f7f7a1ac578c80d4397a7f2b69b40ff.obj",
>     "file_size": 5306476,
>     "url": "https://v3b.fal.media/files/b/0a8c44d5/2W2KRP1DM_-4qI8F_n05b_0f7f7a1ac578c80d4397a7f2b69b40ff.obj"
>   },
>   "material_mtl": {
>     "content_type": "text/plain",
>     "file_name": "material.mtl",
>     "file_size": 157,
>     "url": "https://v3b.fal.media/files/b/0a8c44d5/EHzTxJtHpdIliaMNnEke-_material.mtl"
>   },
>   "texture": {
>     "content_type": "image/png",
>     "file_name": "material.png",
>     "file_size": 5915609,
>     "url": "https://v3b.fal.media/files/b/0a8c44d5/T0q-P0aqXVG_y7jff-XTa_material.png"
>   },
>   "thumbnail": {
>     "content_type": "image/png",
>     "file_name": "preview.png",
>     "file_size": 281374,
>     "url": "https://v3b.fal.media/files/b/0a8c44d6/D9_IYgpugP0deXvSwBC2J_preview.png"
>   },
>   "model_urls": {
>     "mtl": {
>       "content_type": "text/plain",
>       "file_name": "material.mtl",
>       "file_size": 157,
>       "url": "https://v3b.fal.media/files/b/0a8c44d5/EHzTxJtHpdIliaMNnEke-_material.mtl"
>     },
>     "obj": {
>       "content_type": "model/obj",
>       "file_name": "0f7f7a1ac578c80d4397a7f2b69b40ff.obj",
>       "file_size": 5306476,
>       "url": "https://v3b.fal.media/files/b/0a8c44d5/2W2KRP1DM_-4qI8F_n05b_0f7f7a1ac578c80d4397a7f2b69b40ff.obj"
>     },
>     "texture": {
>       "content_type": "image/png",
>       "file_name": "material.png",
>       "file_size": 5915609,
>       "url": "https://v3b.fal.media/files/b/0a8c44d5/T0q-P0aqXVG_y7jff-XTa_material.png"
>     }
>   }
> }
> ```

## Related

* [Hunyuan 3D Pro Image to 3D](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-pro-image-to-3d) — 3D
* [Hunyuan 3D Pro Text to 3D](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-pro-text-to-3d) — 3D
* [Hunyuan 3d](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d) — 3D
* [Hunyuan 3D Smart Topology](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-smart-topology) — 3D
* [Hunyuan 3D Part Splitter](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-part-splitter) — 3D
* [Hunyuan 3D Rapid Image to 3D](https://fal.ai/docs/model-api-reference/3d-api/hunyuan-3d-rapid-image-to-3d) — 3D
