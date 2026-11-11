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

# Topaz Upscale API

> API reference for Topaz Upscale. Use the powerful and accurate topaz image enhancer to enhance your images.

**Endpoint:** `POST https://fal.run/fal-ai/topaz/upscale/image`
**Endpoint ID:** `fal-ai/topaz/upscale/image`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/topaz/upscale/image/playground)
>
> Run this model interactively with your own prompts.

### Quick Start

```python title="Python" theme={null}
import fal_client

def on_queue_update(update):
    if isinstance(update, fal_client.InProgress):
        for log in update.logs:
           print(log["message"])

result = fal_client.subscribe(
    "fal-ai/topaz/upscale/image",
    arguments={
        "image_url": "https://storage.googleapis.com/falserverless/model_tests/codeformer/codeformer_poor_1.jpeg"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/topaz/upscale/image", {
  input: {
      image_url: "https://storage.googleapis.com/falserverless/model_tests/codeformer/codeformer_poor_1.jpeg"
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
  --url https://fal.run/fal-ai/topaz/upscale/image \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "image_url": "https://storage.googleapis.com/falserverless/model_tests/codeformer/codeformer_poor_1.jpeg"
}'
```

### Input Schema

**model** `ModelEnum` (default: Standard V2)

Model to use for image enhancement. Default value: `"Standard V2"`

Possible values: `Low Resolution V2`, `Standard V2`, `CGI`, `High Fidelity V2`, `Text Refine`, `Recovery`, `Redefine`, `Recovery V2`, `Standard MAX`, `Wonder`

**upscale\_factor** `float` (default: 2)

Factor to upscale the image by (e.g. 2.0 doubles width and height) Default value: `2`

Range: `1` to `4`

**crop\_to\_fill** `boolean` (default: false)

**image\_url** `string` *required*

Url of the image to be upscaled

**output\_format** `OutputFormatEnum` (default: jpeg)

Output format of the upscaled image. Default value: `"jpeg"`

Possible values: `jpeg`, `png`

**subject\_detection** `SubjectDetectionEnum` (default: All)

Subject detection mode for the image enhancement. Applies to standard enhance and Recovery V2 models. Default value: `"All"`

Possible values: `All`, `Foreground`, `Background`

**face\_enhancement** `boolean` (default: true)

Whether to apply face enhancement to the image. Applies to standard enhance and Recovery V2 models. Default value: `true`

**face\_enhancement\_creativity** `float` (default: 0)

Creativity level for face enhancement. 0.0 means no creativity, 1.0 means maximum creativity. Ignored if face enhancement is disabled.

Range: `0` to `1`

**face\_enhancement\_strength** `float` (default: 0.8)

Strength of the face enhancement. 0.0 means no enhancement, 1.0 means maximum enhancement. Ignored if face enhancement is disabled. Default value: `0.8`

Range: `0` to `1`

**sharpen** `float`

Sharpening level (0.0-1.0). Applies to Standard V2, Low Resolution V2, CGI, High Fidelity V2, Text Refine, and Redefine models.

Range: `0` to `1`

**denoise** `float`

Denoising level (0.0-1.0). Applies to Standard V2, Low Resolution V2, CGI, High Fidelity V2, Text Refine, and Redefine models.

Range: `0` to `1`

**fix\_compression** `float`

Compression artifact removal level (0.0-1.0). Applies to Standard V2, Low Resolution V2, High Fidelity V2, and Text Refine models.

Range: `0` to `1`

**strength** `float`

Enhancement strength (0.01-1.0). Applies to Text Refine model only.

Range: `0.01` to `1`

**creativity** `integer`

Creativity level for generative upscaling (1-6). Higher values produce more creative/hallucinated details. Applies to Redefine model only.

Range: `1` to `6`

**texture** `integer`

Texture detail level for generative upscaling (1-5). Applies to Redefine model only.

Range: `1` to `5`

**prompt** `string`

Text prompt to guide generative upscaling (max 1024 chars). Applies to Redefine model only.

**autoprompt** `boolean`

Enable automatic prompt generation for generative upscaling. Applies to Redefine model only.

**detail** `float`

Detail recovery level (0.0-1.0). Applies to Recovery V2 model only.

Range: `0` to `1`

### Output Schema

**image** `File` *required*

The upscaled image.

### Input Example

```json theme={null}
{
  "model": "Standard V2",
  "upscale_factor": 2,
  "crop_to_fill": false,
  "image_url": "https://storage.googleapis.com/falserverless/model_tests/codeformer/codeformer_poor_1.jpeg",
  "output_format": "jpeg",
  "subject_detection": "All",
  "face_enhancement": true,
  "face_enhancement_creativity": 0,
  "face_enhancement_strength": 0.8
}
```

### Output Example

```json theme={null}
{
  "image": {
    "url": "",
    "content_type": "image/png",
    "file_name": "z9RV14K95DvU.png",
    "file_size": 4404019
  }
}
```

## Limitations

* `upscale_factor` range: 1 to 4
* `output_format` restricted to: `jpeg`, `png`
* `subject_detection` restricted to: `All`, `Foreground`, `Background`
* `face_enhancement_creativity` range: 0 to 1
* `face_enhancement_strength` range: 0 to 1
* `sharpen` range: 0 to 1
* `denoise` range: 0 to 1
* `fix_compression` range: 0 to 1
* `strength` range: 0.01 to 1
* `creativity` range: 1 to 6
* `texture` range: 1 to 5
* `detail` range: 0 to 1
