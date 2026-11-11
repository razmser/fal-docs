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

# Bytedance Seedance V1 Pro Image To Video API

> API reference for Bytedance Seedance V1 Pro Image To Video. Seedance 1.0 Pro, a high quality video generation model developed by Bytedance.

**Endpoint:** `POST https://fal.run/fal-ai/bytedance/seedance/v1/pro/image-to-video`
**Endpoint ID:** `fal-ai/bytedance/seedance/v1/pro/image-to-video`

> [card] [Try it in the Playground](https://fal.ai/models/fal-ai/bytedance/seedance/v1/pro/image-to-video/playground)
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
    "fal-ai/bytedance/seedance/v1/pro/image-to-video",
    arguments={
        "prompt": "A skier glides over fresh snow, joyously smiling while kicking up large clouds of snow as he turns. Accelerating gradually down the slope, the camera moves smoothly alongside.",
        "image_url": "https://storage.googleapis.com/falserverless/example_inputs/seedance_pro_i2v_img.jpg"
    },
    with_logs=True,
    on_queue_update=on_queue_update,
)
print(result)
```

```javascript title="JavaScript" theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/bytedance/seedance/v1/pro/image-to-video", {
  input: {
      prompt: "A skier glides over fresh snow, joyously smiling while kicking up large clouds of snow as he turns. Accelerating gradually down the slope, the camera moves smoothly alongside.",
      image_url: "https://storage.googleapis.com/falserverless/example_inputs/seedance_pro_i2v_img.jpg"
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
  --url https://fal.run/fal-ai/bytedance/seedance/v1/pro/image-to-video \
  --header "Authorization: Key $FAL_KEY" \
  --header "Content-Type: application/json" \
  --data '{
  "prompt": "A skier glides over fresh snow, joyously smiling while kicking up large clouds of snow as he turns. Accelerating gradually down the slope, the camera moves smoothly alongside.",
  "image_url": "https://storage.googleapis.com/falserverless/example_inputs/seedance_pro_i2v_img.jpg"
}'
```

## Seedance: Professional Image-to-Video Generation

Transform static images into fluid, natural-looking videos with Seedance, ByteDance's advanced AI motion generation model. Whether you're creating engaging social media content, dynamic marketing materials, or creative visual projects, Seedance helps you bring your images to life with sophisticated motion synthesis.

### Key Capabilities

Seedance excels at creating natural motion from single images while preserving visual quality and temporal consistency. The model understands complex scenes and generates realistic movement patterns that respect physical constraints and object relationships.

Create compelling video content from:

* Portrait photos with natural facial expressions and movements
* Landscape scenes with flowing water, swaying trees, and dynamic skies
* Product shots with smooth rotations and transitions
* Abstract art with creative motion effects

### Getting Started

Setting up Seedance is straightforward. First, install the fal client library:

```bash theme={null}
# For Node.js
npm install --save @fal-ai/client

# For Python
pip install fal-client
```

Configure your API credentials:

```typescript theme={null}
import { fal } from "@fal-ai/client";

fal.config({
  credentials: "YOUR_FAL_KEY_HERE"
});
```

### Making Your First API Call

Generate your first video with this simple example:

```typescript theme={null}
const result = await fal.subscribe("fal-ai/bytedance/seedance/v1/pro/image-to-video", {
  input: {
    image_url: "YOUR_IMAGE_URL"
  }
});
```

### Advanced Usage

Seedance offers fine-grained control over video generation through various parameters:

#### Motion Control

Control the intensity and nature of movement with configurable parameters. The model features a wide dynamic range, enabling smooth generation of large-scale movements while maintaining stability and physical realism.

#### Duration and Quality

Adjust video length and quality settings to match your needs. The Pro model supports 1080p output, while the Lite model provides 720p resolution. Both models typically generate 5-second videos.

### Best Practices

For optimal results:

* Use high-quality source images with good lighting and clear subjects
* Accepted file types include: jpg, jpeg, png, webp, gif, avif
* Consider the intended motion when composing your input image
* Start with default parameters and adjust gradually
* Test different configurations to find the right balance for your content

### Technical Specifications

Processing Capabilities:

* Input Resolution: Supports various input formats and sizes
* Output Formats: MP4 video
* Output Resolution: Up to 1080p (Pro), 720p (Lite)
* Frame Rate: Standard video frame rates
* Processing Time: Varies based on complexity and queue

### Pricing and Usage

Each 1080p 5 second video costs roughly $0.74 (Pro version). For other resolutions, 1 million video tokens costs $3.0. The Lite version costs $0.18 per 720p 5 second video, with 1 million video tokens costing $1.8.

Pricing calculation: `tokens(video) = (height x width x FPS x duration) / 1024`

Our transparent, usage-based pricing scales with your needs:

* Pay-per-use model with no subscription required
* Pro version for high-quality 1080p output
* Lite version for cost-effective 720p generation
* No hidden fees or minimum commitments

### Implementation Support

Our comprehensive support resources help you integrate Seedance successfully:

#### Documentation

Access detailed API references, code examples, and integration guides in our documentation portal at docs.fal.ai

#### Client Libraries

We have support via official client libraries for JavaScript, Python, Swift, Java, Kotlin, and Dart (Flutter)

#### Sample Applications

Explore reference implementations and example projects demonstrating common use cases and implementation patterns

For detailed pricing information, visit [fal.ai/pricing](https://fal.ai/pricing) or contact [support@fal.ai](mailto:support@fal.ai) for enterprise solutions.

## Related

* [Seedance 1.0 Lite](https://fal.ai/docs/model-api-reference/video-generation-api/seedance-1.0-lite) — Video Generation
* [Bytedance](https://fal.ai/docs/model-api-reference/video-generation-api/bytedance) — Video Generation
* [Seedance 1.0 Pro](https://fal.ai/docs/model-api-reference/video-generation-api/seedance-1.0-pro) — Video Generation

## Capabilities

* Text prompt input
* Aspect ratio control
* Duration control
* Reproducible generation (seed)
* Safety checker
* Frame count control
* Image input

## API Reference

### Input Schema

**prompt** `string` *required*

The text prompt used to generate the video

**aspect\_ratio** `AspectRatioEnum` (default: auto)

The aspect ratio of the generated video Default value: `"auto"`

Possible values: `21:9`, `16:9`, `4:3`, `1:1`, `3:4`, `9:16`, `auto`

**resolution** `ResolutionEnum` (default: 1080p)

Video resolution - 480p for faster generation, 720p for balance, 1080p for higher quality Default value: `"1080p"`

Possible values: `480p`, `720p`, `1080p`

**duration** `DurationEnum` (default: 5)

Duration of the video in seconds Default value: `"5"`

Possible values: `2`, `3`, `4`, `5`, `6`, `7`, `8`, `9`, `10`, `11`, `12`

**camera\_fixed** `boolean` (default: false)

Whether to fix the camera position

**seed** `integer`

Random seed to control video generation. Use -1 for random.

**enable\_safety\_checker** `boolean` (default: true)

If set to true, the safety checker will be enabled. Default value: `true`

**num\_frames** `integer`

The number of frames to generate. If provided, will override duration.

Range: `29` to `289`

**image\_url** `string` *required*

The URL of the image used to generate video

**end\_image\_url** `string`

The URL of the image the video ends with. Defaults to None.

### Output Schema

**video** `File` *required*

Generated video file

**seed** `integer` *required*

Seed used for generation

## Input Example

```json theme={null}
{
  "prompt": "A skier glides over fresh snow, joyously smiling while kicking up large clouds of snow as he turns. Accelerating gradually down the slope, the camera moves smoothly alongside.",
  "aspect_ratio": "auto",
  "resolution": "1080p",
  "duration": "5",
  "camera_fixed": false,
  "enable_safety_checker": true,
  "image_url": "https://storage.googleapis.com/falserverless/example_inputs/seedance_pro_i2v_img.jpg"
}
```

## Output Example

```json theme={null}
{
  "video": {
    "url": "https://storage.googleapis.com/falserverless/example_inputs/seedance_pro_i2v.mp4"
  },
  "seed": 42
}
```

## Limitations

* `aspect_ratio` restricted to: `21:9`, `16:9`, `4:3`, `1:1`, `3:4`, `9:16`, `auto`
* `resolution` restricted to: `480p`, `720p`, `1080p`
* `num_frames` range: 29 to 289
* Content moderation via safety checker
