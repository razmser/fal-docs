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

# Bytedance Seedream V4 API

> API reference for Bytedance Seedream V4. A new-generation image creation model ByteDance, Seedream 4.0 integrates image generation and image editing capabilities into a single, unified architecture.

> [tab] Edit
>
> **Endpoint:** `POST https://fal.run/fal-ai/bytedance/seedream/v4/edit`
> **Endpoint ID:** `fal-ai/bytedance/seedream/v4/edit`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/bytedance/seedream/v4/edit/playground)
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
>     "fal-ai/bytedance/seedream/v4/edit",
>     arguments={
>         "prompt": "Dress the model in the clothes and hat. Add a cat to the scene and change the background to a Victorian era building.",
>         "image_urls": [
>             "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_1.png",
>             "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_2.png",
>             "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_3.png",
>             "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_4.png"
>         ]
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
> const result = await fal.subscribe("fal-ai/bytedance/seedream/v4/edit", {
>   input: {
>       prompt: "Dress the model in the clothes and hat. Add a cat to the scene and change the background to a Victorian era building.",
>       image_urls: [
>         "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_1.png",
>         "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_2.png",
>         "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_3.png",
>         "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_4.png"
>       ]
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
>   --url https://fal.run/fal-ai/bytedance/seedream/v4/edit \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Dress the model in the clothes and hat. Add a cat to the scene and change the background to a Victorian era building.",
>   "image_urls": [
>     "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_1.png",
>     "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_2.png",
>     "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_3.png",
>     "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_4.png"
>   ]
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The text prompt used to edit the image
>
> **image\_size** `ImageSize | Enum` (default: \[object Object])
>
> The size of the generated image. The minimum total image area is 921600 pixels. Failing this, the image size will be adjusted to by scaling it up, while maintaining the aspect ratio.
>
> Possible values: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`, `auto`, `auto_2K`, `auto_4K`
>
> **num\_images** `integer` (default: 1)
>
> Number of separate model generations to be run with the prompt. Default value: `1`
>
> Range: `1` to `6`
>
> **max\_images** `integer` (default: 1)
>
> If set to a number greater than one, enables multi-image generation. The model will potentially return up to `max_images` images every generation, and in total, `num_images` generations will be carried out. In total, the number of images generated will be between `num_images` and `max_images*num_images`. The total number of images (image inputs + image outputs) must not exceed 15 Default value: `1`
>
> Range: `1` to `6`
>
> **seed** `integer`
>
> Random seed to control the stochasticity of image generation.
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **enable\_safety\_checker** `boolean` (default: true)
>
> If set to true, the safety checker will be enabled. Default value: `true`
>
> **enhance\_prompt\_mode** `EnhancePromptModeEnum` (default: standard)
>
> The mode to use for enhancing prompt enhancement. Standard mode provides higher quality results but takes longer to generate. Fast mode provides average quality results but takes less time to generate. Default value: `"standard"`
>
> Possible values: `standard`, `fast`
>
> **image\_urls** `list\<string\>` *required*
>
> List of URLs of input images for editing. Presently, up to 10 image inputs are allowed. If over 10 images are sent, only the last 10 will be used.
>
> ### Output Schema
>
> **images** `list\<Image\>` *required*
>
> Generated images
>
> **seed** `integer` *required*
>
> Seed used for generation
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "Dress the model in the clothes and hat. Add a cat to the scene and change the background to a Victorian era building.",
>   "image_size": {
>     "height": 2160,
>     "width": 3840
>   },
>   "num_images": 1,
>   "max_images": 1,
>   "sync_mode": false,
>   "enable_safety_checker": true,
>   "enhance_prompt_mode": "standard",
>   "image_urls": [
>     "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_1.png",
>     "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_2.png",
>     "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_3.png",
>     "https://storage.googleapis.com/falserverless/example_inputs/seedream4_edit_input_4.png"
>   ]
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "images": [
>     {
>       "url": "https://storage.googleapis.com/falserverless/example_outputs/seedream4_edit_output.png"
>     }
>   ],
>   "seed": 746406749
> }
> ```

> [tab] Text To Image
>
> **Endpoint:** `POST https://fal.run/fal-ai/bytedance/seedream/v4/text-to-image`
> **Endpoint ID:** `fal-ai/bytedance/seedream/v4/text-to-image`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/bytedance/seedream/v4/text-to-image/playground)
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
>     "fal-ai/bytedance/seedream/v4/text-to-image",
>     arguments={
>         "prompt": "A trendy restaurant with a digital menu board displaying \"Seedream 4.0 is available on fal\" in elegant script, with diners enjoying their meals."
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
> const result = await fal.subscribe("fal-ai/bytedance/seedream/v4/text-to-image", {
>   input: {
>       prompt: "A trendy restaurant with a digital menu board displaying \"Seedream 4.0 is available on fal\" in elegant script, with diners enjoying their meals."
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
>   --url https://fal.run/fal-ai/bytedance/seedream/v4/text-to-image \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A trendy restaurant with a digital menu board displaying \"Seedream 4.0 is available on fal\" in elegant script, with diners enjoying their meals."
> }'
> ```
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> The text prompt used to generate the image
>
> **image\_size** `ImageSize | Enum` (default: \[object Object])
>
> The size of the generated image. Total pixels must be between 960x960 and 4096x4096.
>
> Possible values: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`, `auto`, `auto_2K`, `auto_4K`
>
> **num\_images** `integer` (default: 1)
>
> Number of separate model generations to be run with the prompt. Default value: `1`
>
> Range: `1` to `6`
>
> **max\_images** `integer` (default: 1)
>
> If set to a number greater than one, enables multi-image generation. The model will potentially return up to `max_images` images every generation, and in total, `num_images` generations will be carried out. In total, the number of images generated will be between `num_images` and `max_images*num_images`. Default value: `1`
>
> Range: `1` to `6`
>
> **seed** `integer`
>
> Random seed to control the stochasticity of image generation.
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **enable\_safety\_checker** `boolean` (default: true)
>
> If set to true, the safety checker will be enabled. Default value: `true`
>
> **enhance\_prompt\_mode** `EnhancePromptModeEnum` (default: standard)
>
> The mode to use for enhancing prompt enhancement. Standard mode provides higher quality results but takes longer to generate. Fast mode provides average quality results but takes less time to generate. Default value: `"standard"`
>
> Possible values: `standard`, `fast`
>
> ### Output Schema
>
> **images** `list\<Image\>` *required*
>
> Generated images
>
> **seed** `integer` *required*
>
> Seed used for generation
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "A trendy restaurant with a digital menu board displaying \"Seedream 4.0 is available on fal\" in elegant script, with diners enjoying their meals.",
>   "image_size": {
>     "height": 4096,
>     "width": 4096
>   },
>   "num_images": 1,
>   "max_images": 1,
>   "sync_mode": false,
>   "enable_safety_checker": true,
>   "enhance_prompt_mode": "standard"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "images": [
>     {
>       "url": "https://storage.googleapis.com/falserverless/example_outputs/seedream4_t2i_output.png"
>     }
>   ],
>   "seed": 746406749
> }
> ```

## Related

* [Bytedance Seedream v4](bytedance-seedream-v4.md) — Image Generation
* [Bytedance](https://fal.ai/docs/model-api-reference/image-generation-api/bytedance) — Image Generation
* [Bytedance Seedream v4 Edit](https://fal.ai/docs/model-api-reference/image-generation-api/bytedance-seedream-v4-edit) — Image Generation

## Limitations

* `num_images` range: 1 to 6
* `max_images` range: 1 to 6
* `enhance_prompt_mode` restricted to: `standard`, `fast`
* Content moderation via safety checker
