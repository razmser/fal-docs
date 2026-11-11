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

# Ideogram Character API

> API reference for Ideogram Character. Generate consistent character appearances across multiple images. Maintain facial features, proportions, and distinctive traits for cohesive storytelling and bran

> [tab] Character
>
> **Endpoint:** `POST https://fal.run/fal-ai/ideogram/character`
> **Endpoint ID:** `fal-ai/ideogram/character`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/ideogram/character/playground)
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
>     "fal-ai/ideogram/character",
>     arguments={
>         "prompt": "Place the woman leisurely enjoying a cup of espresso while relaxing at a sunlit café table in Siena, Italy. The café setting showcases vintage wooden furniture with peeling white paint, aged brick flooring, and sun-bleached stone walls decorated with trailing ivy and vibrant potted geraniums that capture Siena's medieval character. Golden late-morning light streams through overhead, creating soft shadows that highlight the weathered architectural details. The composition appears slightly off-center, conveying the unguarded tranquility and personal intimacy of a peaceful moment savoring the Tuscan morning ambiance.",
>         "reference_image_urls": [
>             "https://v3.fal.media/files/kangaroo/0rinwnj_Kn9Fsu2dK-aKm_image.png"
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
> const result = await fal.subscribe("fal-ai/ideogram/character", {
>   input: {
>       prompt: "Place the woman leisurely enjoying a cup of espresso while relaxing at a sunlit café table in Siena, Italy. The café setting showcases vintage wooden furniture with peeling white paint, aged brick flooring, and sun-bleached stone walls decorated with trailing ivy and vibrant potted geraniums that capture Siena's medieval character. Golden late-morning light streams through overhead, creating soft shadows that highlight the weathered architectural details. The composition appears slightly off-center, conveying the unguarded tranquility and personal intimacy of a peaceful moment savoring the Tuscan morning ambiance.",
>       reference_image_urls: [
>         "https://v3.fal.media/files/kangaroo/0rinwnj_Kn9Fsu2dK-aKm_image.png"
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
>   --url https://fal.run/fal-ai/ideogram/character \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Place the woman leisurely enjoying a cup of espresso while relaxing at a sunlit café table in Siena, Italy. The café setting showcases vintage wooden furniture with peeling white paint, aged brick flooring, and sun-bleached stone walls decorated with trailing ivy and vibrant potted geraniums that capture Siena'\''s medieval character. Golden late-morning light streams through overhead, creating soft shadows that highlight the weathered architectural details. The composition appears slightly off-center, conveying the unguarded tranquility and personal intimacy of a peaceful moment savoring the Tuscan morning ambiance.",
>   "reference_image_urls": [
>     "https://v3.fal.media/files/kangaroo/0rinwnj_Kn9Fsu2dK-aKm_image.png"
>   ]
> }'
> ```
>
> ### Input Schema
>
> **image\_urls** `list\<string\>`
>
> A set of images to use as style references (maximum total size 10MB across all style references). The images should be in JPEG, PNG or WebP format
>
> **rendering\_speed** `RenderingSpeedEnum` (default: BALANCED)
>
> The rendering speed to use. Default value: `"BALANCED"`
>
> Possible values: `TURBO`, `BALANCED`, `QUALITY`
>
> **color\_palette** `ColorPalette`
>
> A color palette for generation, must EITHER be specified via one of the presets (name) or explicitly via hexadecimal representations of the color with optional weights (members)
>
> **style\_codes** `list\<string\>`
>
> A list of 8 character hexadecimal codes representing the style of the image. Cannot be used in conjunction with style\_reference\_images or style
>
> **style** `StyleEnum` (default: AUTO)
>
> The style type to generate with. Cannot be used with style\_codes. Default value: `"AUTO"`
>
> Possible values: `AUTO`, `REALISTIC`, `FICTION`
>
> **expand\_prompt** `boolean` (default: true)
>
> Determine if MagicPrompt should be used in generating the request or not. Default value: `true`
>
> **num\_images** `integer` (default: 1)
>
> Number of images to generate. Default value: `1`
>
> Range: `1` to `8`
>
> **seed** `integer`
>
> Seed for the random number generator
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **prompt** `string` *required*
>
> The prompt to fill the masked part of the image.
>
> **image\_size** `ImageSize | Enum` (default: square\_hd)
>
> The resolution of the generated image Default value: `square_hd`
>
> Possible values: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`
>
> **negative\_prompt** `string`
>
> Description of what to exclude from an image. Descriptions in the prompt take precedence to descriptions in the negative prompt. Default value: `""`
>
> **reference\_image\_urls** `list\<string\>` *required*
>
> A set of images to use as character references. Currently only 1 image is supported, rest will be ignored. (maximum total size 10MB across all character references). The images should be in JPEG, PNG or WebP format
>
> **reference\_mask\_urls** `list\<string\>`
>
> A set of masks to apply to the character references. Currently only 1 mask is supported, rest will be ignored. (maximum total size 10MB across all character references). The masks should be in JPEG, PNG or WebP format
>
> ### Output Schema
>
> **images** `list\<File\>` *required*
>
> **seed** `integer` *required*
>
> Seed used for the random number generator
>
> ### Input Example
>
> ```json theme={null}
> {
>   "rendering_speed": "BALANCED",
>   "style": "AUTO",
>   "expand_prompt": true,
>   "num_images": 1,
>   "sync_mode": false,
>   "prompt": "Place the woman leisurely enjoying a cup of espresso while relaxing at a sunlit café table in Siena, Italy. The café setting showcases vintage wooden furniture with peeling white paint, aged brick flooring, and sun-bleached stone walls decorated with trailing ivy and vibrant potted geraniums that capture Siena's medieval character. Golden late-morning light streams through overhead, creating soft shadows that highlight the weathered architectural details. The composition appears slightly off-center, conveying the unguarded tranquility and personal intimacy of a peaceful moment savoring the Tuscan morning ambiance.",
>   "image_size": "square_hd",
>   "negative_prompt": "",
>   "reference_image_urls": [
>     "https://v3.fal.media/files/kangaroo/0rinwnj_Kn9Fsu2dK-aKm_image.png"
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
>       "url": "https://v3.fal.media/files/monkey/NC_1eo9ecE9fARcxviJ2R_image.png"
>     }
>   ],
>   "seed": 123456
> }
> ```

> [tab] Edit
>
> **Endpoint:** `POST https://fal.run/fal-ai/ideogram/character/edit`
> **Endpoint ID:** `fal-ai/ideogram/character/edit`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/ideogram/character/edit/playground)
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
>     "fal-ai/ideogram/character/edit",
>     arguments={
>         "prompt": "woman holding bag",
>         "image_url": "https://v3.fal.media/files/panda/-LC_gNNV3wUHaGMQT3klE_output.png",
>         "mask_url": "https://v3.fal.media/files/panda/jVDAgSkpsZFDP080ceSZJ_woman_face_mask.png",
>         "reference_image_urls": [
>             "https://v3.fal.media/files/kangaroo/0rinwnj_Kn9Fsu2dK-aKm_image.png"
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
> const result = await fal.subscribe("fal-ai/ideogram/character/edit", {
>   input: {
>       prompt: "woman holding bag",
>       image_url: "https://v3.fal.media/files/panda/-LC_gNNV3wUHaGMQT3klE_output.png",
>       mask_url: "https://v3.fal.media/files/panda/jVDAgSkpsZFDP080ceSZJ_woman_face_mask.png",
>       reference_image_urls: [
>         "https://v3.fal.media/files/kangaroo/0rinwnj_Kn9Fsu2dK-aKm_image.png"
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
>   --url https://fal.run/fal-ai/ideogram/character/edit \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "woman holding bag",
>   "image_url": "https://v3.fal.media/files/panda/-LC_gNNV3wUHaGMQT3klE_output.png",
>   "mask_url": "https://v3.fal.media/files/panda/jVDAgSkpsZFDP080ceSZJ_woman_face_mask.png",
>   "reference_image_urls": [
>     "https://v3.fal.media/files/kangaroo/0rinwnj_Kn9Fsu2dK-aKm_image.png"
>   ]
> }'
> ```
>
> ### Input Schema
>
> **image\_urls** `list\<string\>`
>
> A set of images to use as style references (maximum total size 10MB across all style references). The images should be in JPEG, PNG or WebP format
>
> **rendering\_speed** `RenderingSpeedEnum` (default: BALANCED)
>
> The rendering speed to use. Default value: `"BALANCED"`
>
> Possible values: `TURBO`, `BALANCED`, `QUALITY`
>
> **color\_palette** `ColorPalette`
>
> A color palette for generation, must EITHER be specified via one of the presets (name) or explicitly via hexadecimal representations of the color with optional weights (members)
>
> **style\_codes** `list\<string\>`
>
> A list of 8 character hexadecimal codes representing the style of the image. Cannot be used in conjunction with style\_reference\_images or style
>
> **expand\_prompt** `boolean` (default: true)
>
> Determine if MagicPrompt should be used in generating the request or not. Default value: `true`
>
> **num\_images** `integer` (default: 1)
>
> Number of images to generate. Default value: `1`
>
> Range: `1` to `8`
>
> **seed** `integer`
>
> Seed for the random number generator
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **prompt** `string` *required*
>
> The prompt to fill the masked part of the image.
>
> **image\_url** `string` *required*
>
> The image URL to generate an image from. MUST have the exact same dimensions (width and height) as the mask image.
>
> **mask\_url** `string` *required*
>
> The mask URL to inpaint the image. MUST have the exact same dimensions (width and height) as the input image.
>
> **reference\_image\_urls** `list\<string\>` *required*
>
> A set of images to use as character references. Currently only 1 image is supported, rest will be ignored. (maximum total size 10MB across all character references). The images should be in JPEG, PNG or WebP format
>
> **reference\_mask\_urls** `list\<string\>`
>
> A set of masks to apply to the character references. Currently only 1 mask is supported, rest will be ignored. (maximum total size 10MB across all character references). The masks should be in JPEG, PNG or WebP format
>
> ### Output Schema
>
> **images** `list\<File\>` *required*
>
> **seed** `integer` *required*
>
> Seed used for the random number generator
>
> ### Input Example
>
> ```json theme={null}
> {
>   "rendering_speed": "BALANCED",
>   "expand_prompt": true,
>   "num_images": 1,
>   "sync_mode": false,
>   "prompt": "woman holding bag",
>   "image_url": "https://v3.fal.media/files/panda/-LC_gNNV3wUHaGMQT3klE_output.png",
>   "mask_url": "https://v3.fal.media/files/panda/jVDAgSkpsZFDP080ceSZJ_woman_face_mask.png",
>   "reference_image_urls": [
>     "https://v3.fal.media/files/kangaroo/0rinwnj_Kn9Fsu2dK-aKm_image.png"
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
>       "url": "https://v3.fal.media/files/zebra/JJR2zayRdL3Pg7kr9cFyk_image.png"
>     }
>   ],
>   "seed": 123456
> }
> ```

> [tab] Remix
>
> **Endpoint:** `POST https://fal.run/fal-ai/ideogram/character/remix`
> **Endpoint ID:** `fal-ai/ideogram/character/remix`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/ideogram/character/remix/playground)
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
>     "fal-ai/ideogram/character/remix",
>     arguments={
>         "prompt": "A glamorous portrait photograph of a woman in an elegant ballroom setting. The subject wears a champagne-colored ball gown with a fitted bodice, long sleeves, and a full skirt adorned with delicate lace appliques. The dress features a crystal-embellished hair accessory and pearl drop earrings. The grand staircase has ornate gold railings and leads to an elaborate crystal chandelier hanging from an arched ceiling. The walls are decorated with classical paintings featuring floral motifs. The lighting is warm and dramatic, creating a soft glow throughout the space. The composition is shot in a formal portrait style with the subject positioned on the lower landing of the staircase, looking over her shoulder at the camera.",
>         "image_url": "https://v3.fal.media/files/panda/mcxydS-_4ZjfBWFtgoo2z_XHLsl7khq6dC6Qp3cIdJl08rG0I.avif",
>         "reference_image_urls": [
>             "https://v3.fal.media/files/kangaroo/0rinwnj_Kn9Fsu2dK-aKm_image.png"
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
> const result = await fal.subscribe("fal-ai/ideogram/character/remix", {
>   input: {
>       prompt: "A glamorous portrait photograph of a woman in an elegant ballroom setting. The subject wears a champagne-colored ball gown with a fitted bodice, long sleeves, and a full skirt adorned with delicate lace appliques. The dress features a crystal-embellished hair accessory and pearl drop earrings. The grand staircase has ornate gold railings and leads to an elaborate crystal chandelier hanging from an arched ceiling. The walls are decorated with classical paintings featuring floral motifs. The lighting is warm and dramatic, creating a soft glow throughout the space. The composition is shot in a formal portrait style with the subject positioned on the lower landing of the staircase, looking over her shoulder at the camera.",
>       image_url: "https://v3.fal.media/files/panda/mcxydS-_4ZjfBWFtgoo2z_XHLsl7khq6dC6Qp3cIdJl08rG0I.avif",
>       reference_image_urls: [
>         "https://v3.fal.media/files/kangaroo/0rinwnj_Kn9Fsu2dK-aKm_image.png"
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
>   --url https://fal.run/fal-ai/ideogram/character/remix \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "A glamorous portrait photograph of a woman in an elegant ballroom setting. The subject wears a champagne-colored ball gown with a fitted bodice, long sleeves, and a full skirt adorned with delicate lace appliques. The dress features a crystal-embellished hair accessory and pearl drop earrings. The grand staircase has ornate gold railings and leads to an elaborate crystal chandelier hanging from an arched ceiling. The walls are decorated with classical paintings featuring floral motifs. The lighting is warm and dramatic, creating a soft glow throughout the space. The composition is shot in a formal portrait style with the subject positioned on the lower landing of the staircase, looking over her shoulder at the camera.",
>   "image_url": "https://v3.fal.media/files/panda/mcxydS-_4ZjfBWFtgoo2z_XHLsl7khq6dC6Qp3cIdJl08rG0I.avif",
>   "reference_image_urls": [
>     "https://v3.fal.media/files/kangaroo/0rinwnj_Kn9Fsu2dK-aKm_image.png"
>   ]
> }'
> ```
>
> ### Input Schema
>
> **image\_urls** `list\<string\>`
>
> A set of images to use as style references (maximum total size 10MB across all style references). The images should be in JPEG, PNG or WebP format
>
> **rendering\_speed** `RenderingSpeedEnum` (default: BALANCED)
>
> The rendering speed to use. Default value: `"BALANCED"`
>
> Possible values: `TURBO`, `BALANCED`, `QUALITY`
>
> **color\_palette** `ColorPalette`
>
> A color palette for generation, must EITHER be specified via one of the presets (name) or explicitly via hexadecimal representations of the color with optional weights (members)
>
> **style\_codes** `list\<string\>`
>
> A list of 8 character hexadecimal codes representing the style of the image. Cannot be used in conjunction with style\_reference\_images or style
>
> **style** `StyleEnum` (default: AUTO)
>
> The style type to generate with. Cannot be used with style\_codes. Default value: `"AUTO"`
>
> Possible values: `AUTO`, `REALISTIC`, `FICTION`
>
> **expand\_prompt** `boolean` (default: true)
>
> Determine if MagicPrompt should be used in generating the request or not. Default value: `true`
>
> **num\_images** `integer` (default: 1)
>
> Number of images to generate. Default value: `1`
>
> Range: `1` to `8`
>
> **seed** `integer`
>
> Seed for the random number generator
>
> **sync\_mode** `boolean` (default: false)
>
> If `True`, the media will be returned as a data URI and the output data won't be available in the request history.
>
> **prompt** `string` *required*
>
> The prompt to remix the image with
>
> **image\_url** `string` *required*
>
> The image URL to remix
>
> **strength** `float` (default: 0.8)
>
> Strength of the input image in the remix Default value: `0.8`
>
> Range: `0.01` to `1`
>
> **image\_size** `ImageSize | Enum` (default: square\_hd)
>
> The resolution of the generated image Default value: `square_hd`
>
> Possible values: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`
>
> **negative\_prompt** `string`
>
> Description of what to exclude from an image. Descriptions in the prompt take precedence to descriptions in the negative prompt. Default value: `""`
>
> **reference\_image\_urls** `list\<string\>` *required*
>
> A set of images to use as character references. Currently only 1 image is supported, rest will be ignored. (maximum total size 10MB across all character references). The images should be in JPEG, PNG or WebP format
>
> **reference\_mask\_urls** `list\<string\>`
>
> A set of masks to apply to the character references. Currently only 1 mask is supported, rest will be ignored. (maximum total size 10MB across all character references). The masks should be in JPEG, PNG or WebP format
>
> ### Output Schema
>
> **images** `list\<File\>` *required*
>
> **seed** `integer` *required*
>
> Seed used for the random number generator
>
> ### Input Example
>
> ```json theme={null}
> {
>   "rendering_speed": "BALANCED",
>   "style": "AUTO",
>   "expand_prompt": true,
>   "num_images": 1,
>   "sync_mode": false,
>   "prompt": "A glamorous portrait photograph of a woman in an elegant ballroom setting. The subject wears a champagne-colored ball gown with a fitted bodice, long sleeves, and a full skirt adorned with delicate lace appliques. The dress features a crystal-embellished hair accessory and pearl drop earrings. The grand staircase has ornate gold railings and leads to an elaborate crystal chandelier hanging from an arched ceiling. The walls are decorated with classical paintings featuring floral motifs. The lighting is warm and dramatic, creating a soft glow throughout the space. The composition is shot in a formal portrait style with the subject positioned on the lower landing of the staircase, looking over her shoulder at the camera.",
>   "image_url": "https://v3.fal.media/files/panda/mcxydS-_4ZjfBWFtgoo2z_XHLsl7khq6dC6Qp3cIdJl08rG0I.avif",
>   "strength": 0.8,
>   "image_size": "square_hd",
>   "negative_prompt": "",
>   "reference_image_urls": [
>     "https://v3.fal.media/files/kangaroo/0rinwnj_Kn9Fsu2dK-aKm_image.png"
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
>       "url": "https://v3.fal.media/files/zebra/4F1SvlaPbkZt-Mle4CTH9_image.png"
>     }
>   ],
>   "seed": 123456
> }
> ```

## Related

* [Ideogram V3 Character Edit](https://fal.ai/docs/model-api-reference/image-generation-api/ideogram-v3-character-edit) — Image Generation
* [Ideogram V3 Character Remix](https://fal.ai/docs/model-api-reference/image-generation-api/ideogram-v3-character-remix) — Image Generation
* [Ideogram V3 Character](https://fal.ai/docs/model-api-reference/image-generation-api/ideogram-v3-character) — Image Generation

## Limitations

* `rendering_speed` restricted to: `TURBO`, `BALANCED`, `QUALITY`
* `style` restricted to: `AUTO`, `REALISTIC`, `FICTION`
* `num_images` range: 1 to 8
* `image_size` restricted to: `square_hd`, `square`, `portrait_4_3`, `portrait_16_9`, `landscape_4_3`, `landscape_16_9`
* `strength` range: 0.01 to 1
