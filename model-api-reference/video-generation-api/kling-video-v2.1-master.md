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

# Kling Video V2.1 Master API

> API reference for Kling Video V2.1 Master. Kling 2.1 Master: The premium endpoint for Kling 2.1, designed for top-tier image-to-video generation with unparalleled motion fluidity, cinematic visuals, a

> [tab] Image To Video
>
> **Endpoint:** `POST https://fal.run/fal-ai/kling-video/v2.1/master/image-to-video`
> **Endpoint ID:** `fal-ai/kling-video/v2.1/master/image-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/v2.1/master/image-to-video/playground)
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
>     "fal-ai/kling-video/v2.1/master/image-to-video",
>     arguments={
>         "prompt": "Sunlight dapples through budding branches, illuminating a vibrant tapestry of greens and browns as a pair of robins meticulously weave twigs and mud into a cradle of life, their tiny forms a whirlwind of activity against a backdrop of blossoming spring.  The scene unfolds with a gentle, observational pace, allowing the viewer to fully appreciate the intricate details of nest construction, the soft textures of downy feathers contrasted against the rough bark of the branches, the delicate balance of strength and fragility in their creation.",
>         "image_url": "https://v3.fal.media/files/zebra/9Nrm22YyLojSTPJbZYNhh_image.webp"
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
> const result = await fal.subscribe("fal-ai/kling-video/v2.1/master/image-to-video", {
>   input: {
>       prompt: "Sunlight dapples through budding branches, illuminating a vibrant tapestry of greens and browns as a pair of robins meticulously weave twigs and mud into a cradle of life, their tiny forms a whirlwind of activity against a backdrop of blossoming spring.  The scene unfolds with a gentle, observational pace, allowing the viewer to fully appreciate the intricate details of nest construction, the soft textures of downy feathers contrasted against the rough bark of the branches, the delicate balance of strength and fragility in their creation.",
>       image_url: "https://v3.fal.media/files/zebra/9Nrm22YyLojSTPJbZYNhh_image.webp"
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
>   --url https://fal.run/fal-ai/kling-video/v2.1/master/image-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Sunlight dapples through budding branches, illuminating a vibrant tapestry of greens and browns as a pair of robins meticulously weave twigs and mud into a cradle of life, their tiny forms a whirlwind of activity against a backdrop of blossoming spring.  The scene unfolds with a gentle, observational pace, allowing the viewer to fully appreciate the intricate details of nest construction, the soft textures of downy feathers contrasted against the rough bark of the branches, the delicate balance of strength and fragility in their creation.",
>   "image_url": "https://v3.fal.media/files/zebra/9Nrm22YyLojSTPJbZYNhh_image.webp"
> }'
> ```
>
> ## Examples
>
> > Sunlight streams through the window, illuminating the ancient bonsai tree and the gardener's weathered hands as they delicately prune a wayward branch, the soft rustle of shears a counterpoint to the quiet intensity of the moment.  The close-up reveals the intricate texture of the bark, the deep gre...
>
> <video src="https://v3.fal.media/files/rabbit/fzwNd7kWSLkxFjzuMtrVH_output.mp4" controls width="100%" />
>
> > Bathed in the warm glow of a single, strategically placed lamp, the jeweler’s weathered hands delicately cradle a fiery opal, its iridescent depths shimmering like a captured sunset; each careful turn reveals new constellations of color, while the close-up shot emphasizes the stone’s alluring textur...
>
> <video src="https://v3.fal.media/files/rabbit/PbFnNcVm4oOmIADyoWguL_output.mp4" controls width="100%" />
>
> > Swirls of ivory frosting, smooth as porcelain, rise and fall across a towering six-tiered cake, their delicate peaks catching the warm light, a symphony of creamy white against the rich mahogany of the cake stand.  Each delicate floral arrangement, painstakingly crafted from sugar paste, adds a burs...
>
> <video src="https://v3.fal.media/files/panda/Vv8_-iXA5LeoNjsG6imIc_output.mp4" controls width="100%" />
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> **image\_url** `string` *required*
>
> URL of the image to be used for the video
>
> **duration** `DurationEnum` (default: 5)
>
> The duration of the generated video in seconds Default value: `"5"`
>
> Possible values: `5`, `10`
>
> **negative\_prompt** `string` (default: blur, distort, and low quality)
>
> Default value: `"blur, distort, and low quality"`
>
> **cfg\_scale** `float` (default: 0.5)
>
> The CFG (Classifier Free Guidance) scale is a measure of how close you want
> the model to stick to your prompt. Default value: `0.5`
>
> Range: `0` to `1`
>
> ### Output Schema
>
> **video** `File` *required*
>
> The generated video
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "Sunlight dapples through budding branches, illuminating a vibrant tapestry of greens and browns as a pair of robins meticulously weave twigs and mud into a cradle of life, their tiny forms a whirlwind of activity against a backdrop of blossoming spring.  The scene unfolds with a gentle, observational pace, allowing the viewer to fully appreciate the intricate details of nest construction, the soft textures of downy feathers contrasted against the rough bark of the branches, the delicate balance of strength and fragility in their creation.",
>   "image_url": "https://v3.fal.media/files/zebra/9Nrm22YyLojSTPJbZYNhh_image.webp",
>   "duration": "5",
>   "negative_prompt": "blur, distort, and low quality",
>   "cfg_scale": 0.5
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "url": "https://v3.fal.media/files/rabbit/YuUWKFq508zzWIiQ0i2vt_output.mp4"
>   }
> }
> ```

> [tab] Text To Video
>
> **Endpoint:** `POST https://fal.run/fal-ai/kling-video/v2.1/master/text-to-video`
> **Endpoint ID:** `fal-ai/kling-video/v2.1/master/text-to-video`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/kling-video/v2.1/master/text-to-video/playground)
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
>     "fal-ai/kling-video/v2.1/master/text-to-video",
>     arguments={
>         "prompt": "Warm, earthy tones bathe the scene as the potter's hands, rough and calloused, coax a shapeless lump of clay into a vessel of elegant curves, the slow, deliberate movements highlighted by the subtle shifting light; the clay's cool, damp texture contrasts sharply with the warmth of the potter's touch, creating a captivating interplay between material and maker."
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
> const result = await fal.subscribe("fal-ai/kling-video/v2.1/master/text-to-video", {
>   input: {
>       prompt: "Warm, earthy tones bathe the scene as the potter's hands, rough and calloused, coax a shapeless lump of clay into a vessel of elegant curves, the slow, deliberate movements highlighted by the subtle shifting light; the clay's cool, damp texture contrasts sharply with the warmth of the potter's touch, creating a captivating interplay between material and maker."
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
>   --url https://fal.run/fal-ai/kling-video/v2.1/master/text-to-video \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "prompt": "Warm, earthy tones bathe the scene as the potter'\''s hands, rough and calloused, coax a shapeless lump of clay into a vessel of elegant curves, the slow, deliberate movements highlighted by the subtle shifting light; the clay'\''s cool, damp texture contrasts sharply with the warmth of the potter'\''s touch, creating a captivating interplay between material and maker."
> }'
> ```
>
> ## Examples
>
> > Golden sunlight bathes the bustling street market, illuminating the vendor's deft hands as they knead vibrant dough, the flour dusting the air like a soft, ethereal snow;  the sizzle of the seasoned meat, a rhythmic counterpoint to the chatter of the crowd, creates a symphony of sights and sounds, c...
>
> <video src="https://v3.fal.media/files/elephant/rf9dUthNKPrlmQXKpzhll_output.mp4" controls width="100%" />
>
> > A breathtaking vista unfolds as the camera captures the inky blackness of the night sky mirroring the deep indigo of the ocean, where waves, edged with an ethereal, electric blue glow, crash gently against the shore, their phosphorescent foam leaving trails of shimmering light like liquid stardust.
>
> <video src="https://v3.fal.media/files/lion/bh7MmcDsPws1syU8euzO5_output.mp4" controls width="100%" />
>
> > Molten rivers of incandescent orange and crimson lava, sluggishly tracing paths of destruction across the scorched earth, illuminate the night with an infernal glow;  their viscous surfaces, a tapestry of bubbling textures and crackling crusts, slowly creep onward, leaving behind a trail of solidifi...
>
> <video src="https://v3.fal.media/files/penguin/AdyDUWgDWpw13y2ejsy1M_output.mp4" controls width="100%" />
>
> ### Input Schema
>
> **prompt** `string` *required*
>
> **duration** `DurationEnum` (default: 5)
>
> The duration of the generated video in seconds Default value: `"5"`
>
> Possible values: `5`, `10`
>
> **aspect\_ratio** `AspectRatioEnum` (default: 16:9)
>
> The aspect ratio of the generated video frame Default value: `"16:9"`
>
> Possible values: `16:9`, `9:16`, `1:1`
>
> **negative\_prompt** `string` (default: blur, distort, and low quality)
>
> Default value: `"blur, distort, and low quality"`
>
> **cfg\_scale** `float` (default: 0.5)
>
> The CFG (Classifier Free Guidance) scale is a measure of how close you want
> the model to stick to your prompt. Default value: `0.5`
>
> Range: `0` to `1`
>
> ### Output Schema
>
> **video** `File` *required*
>
> The generated video
>
> ### Input Example
>
> ```json theme={null}
> {
>   "prompt": "Warm, earthy tones bathe the scene as the potter's hands, rough and calloused, coax a shapeless lump of clay into a vessel of elegant curves, the slow, deliberate movements highlighted by the subtle shifting light; the clay's cool, damp texture contrasts sharply with the warmth of the potter's touch, creating a captivating interplay between material and maker.",
>   "duration": "5",
>   "aspect_ratio": "16:9",
>   "negative_prompt": "blur, distort, and low quality",
>   "cfg_scale": 0.5
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "video": {
>     "url": "https://v3.fal.media/files/lion/0wTlhR7GCXFI-_BZXGy99_output.mp4"
>   }
> }
> ```

## Related

* [Kling 2.1 (standard)](https://fal.ai/docs/model-api-reference/video-generation-api/kling-2.1-standard) — Video Generation
* [Kling 2.1 (pro)](https://fal.ai/docs/model-api-reference/video-generation-api/kling-2.1-pro) — Video Generation
* [Kling 2.1 Master](https://fal.ai/docs/model-api-reference/video-generation-api/kling-2.1-master) — Video Generation

## Limitations

* `duration` restricted to: `5`, `10`
* `cfg_scale` range: 0 to 1
* `aspect_ratio` restricted to: `16:9`, `9:16`, `1:1`
