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

# Florence 2 Large API

> API reference for Florence 2 Large. Florence-2 is an advanced vision foundation model that uses a prompt-based approach to handle a wide range of vision and vision-language tasks

> [tab] Object Detection
>
> **Endpoint:** `POST https://fal.run/fal-ai/florence-2-large/object-detection`
> **Endpoint ID:** `fal-ai/florence-2-large/object-detection`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/florence-2-large/object-detection/playground)
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
>     "fal-ai/florence-2-large/object-detection",
>     arguments={
>         "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
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
> const result = await fal.subscribe("fal-ai/florence-2-large/object-detection", {
>   input: {
>       image_url: "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
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
>   --url https://fal.run/fal-ai/florence-2-large/object-detection \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> The URL of the image to be processed.
>
> ### Output Schema
>
> **results** `BoundingBoxes` *required*
>
> Results from the model
>
> **image** `Image`
>
> Processed image
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "results": {
>     "bboxes": [
>       {
>         "label": ""
>       }
>     ]
>   }
> }
> ```

> [tab] Open Vocabulary Detection
>
> **Endpoint:** `POST https://fal.run/fal-ai/florence-2-large/open-vocabulary-detection`
> **Endpoint ID:** `fal-ai/florence-2-large/open-vocabulary-detection`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/florence-2-large/open-vocabulary-detection/playground)
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
>     "fal-ai/florence-2-large/open-vocabulary-detection",
>     arguments={
>         "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>         "text_input": ""
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
> const result = await fal.subscribe("fal-ai/florence-2-large/open-vocabulary-detection", {
>   input: {
>       image_url: "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>       text_input: ""
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
>   --url https://fal.run/fal-ai/florence-2-large/open-vocabulary-detection \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>   "text_input": ""
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> The URL of the image to be processed.
>
> **text\_input** `string` *required*
>
> Text input for the task
>
> ### Output Schema
>
> **results** `BoundingBoxes` *required*
>
> Results from the model
>
> **image** `Image`
>
> Processed image
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>   "text_input": ""
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "results": {
>     "bboxes": [
>       {
>         "label": ""
>       }
>     ]
>   }
> }
> ```

> [tab] Ocr With Region
>
> **Endpoint:** `POST https://fal.run/fal-ai/florence-2-large/ocr-with-region`
> **Endpoint ID:** `fal-ai/florence-2-large/ocr-with-region`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/florence-2-large/ocr-with-region/playground)
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
>     "fal-ai/florence-2-large/ocr-with-region",
>     arguments={
>         "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
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
> const result = await fal.subscribe("fal-ai/florence-2-large/ocr-with-region", {
>   input: {
>       image_url: "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
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
>   --url https://fal.run/fal-ai/florence-2-large/ocr-with-region \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> The URL of the image to be processed.
>
> ### Output Schema
>
> **results** `OCRBoundingBox` *required*
>
> Results from the model
>
> **image** `Image`
>
> Processed image
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "results": {
>     "quad_boxes": [
>       {
>         "label": ""
>       }
>     ]
>   }
> }
> ```

> [tab] Caption To Phrase Grounding
>
> **Endpoint:** `POST https://fal.run/fal-ai/florence-2-large/caption-to-phrase-grounding`
> **Endpoint ID:** `fal-ai/florence-2-large/caption-to-phrase-grounding`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/florence-2-large/caption-to-phrase-grounding/playground)
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
>     "fal-ai/florence-2-large/caption-to-phrase-grounding",
>     arguments={
>         "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>         "text_input": ""
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
> const result = await fal.subscribe("fal-ai/florence-2-large/caption-to-phrase-grounding", {
>   input: {
>       image_url: "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>       text_input: ""
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
>   --url https://fal.run/fal-ai/florence-2-large/caption-to-phrase-grounding \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>   "text_input": ""
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> The URL of the image to be processed.
>
> **text\_input** `string` *required*
>
> Text input for the task
>
> ### Output Schema
>
> **results** `BoundingBoxes` *required*
>
> Results from the model
>
> **image** `Image`
>
> Processed image
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>   "text_input": ""
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "results": {
>     "bboxes": [
>       {
>         "label": ""
>       }
>     ]
>   }
> }
> ```

> [tab] Referring Expression Segmentation
>
> **Endpoint:** `POST https://fal.run/fal-ai/florence-2-large/referring-expression-segmentation`
> **Endpoint ID:** `fal-ai/florence-2-large/referring-expression-segmentation`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/florence-2-large/referring-expression-segmentation/playground)
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
>     "fal-ai/florence-2-large/referring-expression-segmentation",
>     arguments={
>         "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>         "text_input": ""
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
> const result = await fal.subscribe("fal-ai/florence-2-large/referring-expression-segmentation", {
>   input: {
>       image_url: "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>       text_input: ""
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
>   --url https://fal.run/fal-ai/florence-2-large/referring-expression-segmentation \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>   "text_input": ""
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> The URL of the image to be processed.
>
> **text\_input** `string` *required*
>
> Text input for the task
>
> ### Output Schema
>
> **results** `PolygonOutput` *required*
>
> Results from the model
>
> **image** `Image`
>
> Processed image
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>   "text_input": ""
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "results": {
>     "polygons": [
>       {
>         "label": ""
>       }
>     ]
>   }
> }
> ```

> [tab] Dense Region Caption
>
> **Endpoint:** `POST https://fal.run/fal-ai/florence-2-large/dense-region-caption`
> **Endpoint ID:** `fal-ai/florence-2-large/dense-region-caption`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/florence-2-large/dense-region-caption/playground)
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
>     "fal-ai/florence-2-large/dense-region-caption",
>     arguments={
>         "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
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
> const result = await fal.subscribe("fal-ai/florence-2-large/dense-region-caption", {
>   input: {
>       image_url: "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
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
>   --url https://fal.run/fal-ai/florence-2-large/dense-region-caption \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> The URL of the image to be processed.
>
> ### Output Schema
>
> **results** `BoundingBoxes` *required*
>
> Results from the model
>
> **image** `Image`
>
> Processed image
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "results": {
>     "bboxes": [
>       {
>         "label": ""
>       }
>     ]
>   }
> }
> ```

> [tab] Region To Segmentation
>
> **Endpoint:** `POST https://fal.run/fal-ai/florence-2-large/region-to-segmentation`
> **Endpoint ID:** `fal-ai/florence-2-large/region-to-segmentation`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/florence-2-large/region-to-segmentation/playground)
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
>     "fal-ai/florence-2-large/region-to-segmentation",
>     arguments={
>         "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>         "region": {
>             "x1": 100,
>             "x2": 200,
>             "y1": 100,
>             "y2": 200
>         }
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
> const result = await fal.subscribe("fal-ai/florence-2-large/region-to-segmentation", {
>   input: {
>       image_url: "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>       region: {
>         x1: 100,
>         x2: 200,
>         y1: 100,
>         y2: 200
>       }
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
>   --url https://fal.run/fal-ai/florence-2-large/region-to-segmentation \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>   "region": {
>     "x1": 100,
>     "x2": 200,
>     "y1": 100,
>     "y2": 200
>   }
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> The URL of the image to be processed.
>
> **region** `Region` *required*
>
> The user input coordinates
>
> ### Output Schema
>
> **results** `PolygonOutput` *required*
>
> Results from the model
>
> **image** `Image`
>
> Processed image
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg",
>   "region": {
>     "x1": 100,
>     "x2": 200,
>     "y1": 100,
>     "y2": 200
>   }
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "results": {
>     "polygons": [
>       {
>         "label": ""
>       }
>     ]
>   }
> }
> ```

> [tab] Region Proposal
>
> **Endpoint:** `POST https://fal.run/fal-ai/florence-2-large/region-proposal`
> **Endpoint ID:** `fal-ai/florence-2-large/region-proposal`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/florence-2-large/region-proposal/playground)
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
>     "fal-ai/florence-2-large/region-proposal",
>     arguments={
>         "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
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
> const result = await fal.subscribe("fal-ai/florence-2-large/region-proposal", {
>   input: {
>       image_url: "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
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
>   --url https://fal.run/fal-ai/florence-2-large/region-proposal \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> The URL of the image to be processed.
>
> ### Output Schema
>
> **results** `BoundingBoxes` *required*
>
> Results from the model
>
> **image** `Image`
>
> Processed image
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/transformers/tasks/car.jpg"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "results": {
>     "bboxes": [
>       {
>         "label": ""
>       }
>     ]
>   }
> }
> ```

## Related

* [Florence-2 Large](../vision-api/florence-2-large.md) — Vision
* [Florence-2 Large](florence-2-large.md) — Image Generation
