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

> [tab] More Detailed Caption
>
> **Endpoint:** `POST https://fal.run/fal-ai/florence-2-large/more-detailed-caption`
> **Endpoint ID:** `fal-ai/florence-2-large/more-detailed-caption`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/florence-2-large/more-detailed-caption/playground)
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
>     "fal-ai/florence-2-large/more-detailed-caption",
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
> const result = await fal.subscribe("fal-ai/florence-2-large/more-detailed-caption", {
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
>   --url https://fal.run/fal-ai/florence-2-large/more-detailed-caption \
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
> **results** `string` *required*
>
> Results from the model
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
>   "results": ""
> }
> ```

> [tab] Detailed Caption
>
> **Endpoint:** `POST https://fal.run/fal-ai/florence-2-large/detailed-caption`
> **Endpoint ID:** `fal-ai/florence-2-large/detailed-caption`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/florence-2-large/detailed-caption/playground)
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
>     "fal-ai/florence-2-large/detailed-caption",
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
> const result = await fal.subscribe("fal-ai/florence-2-large/detailed-caption", {
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
>   --url https://fal.run/fal-ai/florence-2-large/detailed-caption \
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
> **results** `string` *required*
>
> Results from the model
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
>   "results": ""
> }
> ```

> [tab] Caption
>
> **Endpoint:** `POST https://fal.run/fal-ai/florence-2-large/caption`
> **Endpoint ID:** `fal-ai/florence-2-large/caption`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/florence-2-large/caption/playground)
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
>     "fal-ai/florence-2-large/caption",
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
> const result = await fal.subscribe("fal-ai/florence-2-large/caption", {
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
>   --url https://fal.run/fal-ai/florence-2-large/caption \
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
> **results** `string` *required*
>
> Results from the model
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
>   "results": ""
> }
> ```

> [tab] Ocr
>
> **Endpoint:** `POST https://fal.run/fal-ai/florence-2-large/ocr`
> **Endpoint ID:** `fal-ai/florence-2-large/ocr`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/florence-2-large/ocr/playground)
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
>     "fal-ai/florence-2-large/ocr",
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
> const result = await fal.subscribe("fal-ai/florence-2-large/ocr", {
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
>   --url https://fal.run/fal-ai/florence-2-large/ocr \
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
> **results** `string` *required*
>
> Results from the model
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
>   "results": ""
> }
> ```

> [tab] Region To Description
>
> **Endpoint:** `POST https://fal.run/fal-ai/florence-2-large/region-to-description`
> **Endpoint ID:** `fal-ai/florence-2-large/region-to-description`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/florence-2-large/region-to-description/playground)
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
>     "fal-ai/florence-2-large/region-to-description",
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
> const result = await fal.subscribe("fal-ai/florence-2-large/region-to-description", {
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
>   --url https://fal.run/fal-ai/florence-2-large/region-to-description \
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
> **results** `string` *required*
>
> Results from the model
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
>   "results": ""
> }
> ```

> [tab] Region To Category
>
> **Endpoint:** `POST https://fal.run/fal-ai/florence-2-large/region-to-category`
> **Endpoint ID:** `fal-ai/florence-2-large/region-to-category`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/florence-2-large/region-to-category/playground)
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
>     "fal-ai/florence-2-large/region-to-category",
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
> const result = await fal.subscribe("fal-ai/florence-2-large/region-to-category", {
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
>   --url https://fal.run/fal-ai/florence-2-large/region-to-category \
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
> **results** `string` *required*
>
> Results from the model
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
>   "results": ""
> }
> ```

## Related

* [Florence-2 Large](florence-2-large.md) — Vision
* [Florence-2 Large](../image-generation-api/florence-2-large.md) — Image Generation
