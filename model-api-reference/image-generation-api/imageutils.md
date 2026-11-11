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

# Imageutils API

> API reference for Imageutils. Remove the background from an image.

> [tab] Rembg
>
> **Endpoint:** `POST https://fal.run/fal-ai/imageutils/rembg`
> **Endpoint ID:** `fal-ai/imageutils/rembg`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/imageutils/rembg/playground)
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
>     "fal-ai/imageutils/rembg",
>     arguments={
>         "image_url": "https://storage.googleapis.com/falserverless/model_tests/remove_background/elephant.jpg"
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
> const result = await fal.subscribe("fal-ai/imageutils/rembg", {
>   input: {
>       image_url: "https://storage.googleapis.com/falserverless/model_tests/remove_background/elephant.jpg"
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
>   --url https://fal.run/fal-ai/imageutils/rembg \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://storage.googleapis.com/falserverless/model_tests/remove_background/elephant.jpg"
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> Input image url.
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **crop\_to\_bbox** `boolean` (default: false)
>
> If set to true, the resulting image be cropped to a bounding box around the subject
>
> ### Output Schema
>
> **image** `Image` *required*
>
> Background removed image.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://storage.googleapis.com/falserverless/model_tests/remove_background/elephant.jpg",
>   "sync_mode": false,
>   "crop_to_bbox": false
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "image": {
>     "url": "",
>     "content_type": "image/png",
>     "file_name": "z9RV14K95DvU.png",
>     "file_size": 4404019,
>     "width": 1024,
>     "height": 1024
>   }
> }
> ```

> [tab] Depth
>
> **Endpoint:** `POST https://fal.run/fal-ai/imageutils/depth`
> **Endpoint ID:** `fal-ai/imageutils/depth`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/imageutils/depth/playground)
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
>     "fal-ai/imageutils/depth",
>     arguments={
>         "image_url": "https://storage.googleapis.com/falserverless/model_tests/remove_background/elephant.jpg"
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
> const result = await fal.subscribe("fal-ai/imageutils/depth", {
>   input: {
>       image_url: "https://storage.googleapis.com/falserverless/model_tests/remove_background/elephant.jpg"
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
>   --url https://fal.run/fal-ai/imageutils/depth \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://storage.googleapis.com/falserverless/model_tests/remove_background/elephant.jpg"
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> Input image url.
>
> **a** `float` (default: 6.283185307179586)
>
> a Default value: `6.283185307179586`
>
> **bg\_th** `float` (default: 0.1)
>
> bg\_th Default value: `0.1`
>
> **depth\_and\_normal** `boolean` (default: false)
>
> depth\_and\_normal
>
> ### Output Schema
>
> **image** `Image` *required*
>
> The depth map.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://storage.googleapis.com/falserverless/model_tests/remove_background/elephant.jpg",
>   "a": 6.283185307179586,
>   "bg_th": 0.1,
>   "depth_and_normal": false
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "image": {
>     "url": "",
>     "content_type": "image/png",
>     "file_name": "z9RV14K95DvU.png",
>     "file_size": 4404019,
>     "width": 1024,
>     "height": 1024
>   }
> }
> ```

> [tab] Marigold Depth
>
> **Endpoint:** `POST https://fal.run/fal-ai/imageutils/marigold-depth`
> **Endpoint ID:** `fal-ai/imageutils/marigold-depth`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/imageutils/marigold-depth/playground)
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
>     "fal-ai/imageutils/marigold-depth",
>     arguments={
>         "image_url": "https://storage.googleapis.com/falserverless/model_tests/remove_background/elephant.jpg"
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
> const result = await fal.subscribe("fal-ai/imageutils/marigold-depth", {
>   input: {
>       image_url: "https://storage.googleapis.com/falserverless/model_tests/remove_background/elephant.jpg"
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
>   --url https://fal.run/fal-ai/imageutils/marigold-depth \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://storage.googleapis.com/falserverless/model_tests/remove_background/elephant.jpg"
> }'
> ```
>
> ## Examples
>
> ![Example output from Marigold Depth Estimation](https://v3.fal.media/files/rabbit/N0GgfUE25qCp850ji09pY_209fe579ff1045889f7f3513e5a6401f.png)
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> Input image url.
>
> **num\_inference\_steps** `integer` (default: 10)
>
> Number of denoising steps. Defaults to `10`. The higher the number, the more accurate the result, but the slower the inference. Default value: `10`
>
> Range: `2` to `50`
>
> **ensemble\_size** `integer` (default: 10)
>
> Number of predictions to average over. Defaults to `10`. The higher the number, the more accurate the result, but the slower the inference. Default value: `10`
>
> Range: `2` to `50`
>
> **processing\_res** `integer` (default: 0)
>
> Maximum processing resolution. Defaults `0` which means it uses the size of the input image.
>
> Range: `0` to `2048`
>
> ### Output Schema
>
> **image** `Image` *required*
>
> The depth map.
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://storage.googleapis.com/falserverless/model_tests/remove_background/elephant.jpg",
>   "num_inference_steps": 10,
>   "ensemble_size": 10,
>   "processing_res": 0
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "image": {
>     "url": "",
>     "content_type": "image/png",
>     "file_name": "z9RV14K95DvU.png",
>     "file_size": 4404019,
>     "width": 1024,
>     "height": 1024
>   }
> }
> ```

## Limitations

* `num_inference_steps` range: 2 to 50
* `ensemble_size` range: 2 to 50
* `processing_res` range: 0 to 2048
