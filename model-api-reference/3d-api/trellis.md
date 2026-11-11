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

# Trellis API

> API reference for Trellis. Generate 3D models from your images using Trellis. A native 3D generative model enabling versatile and high-quality 3D asset creation.

> [tab] Trellis
>
> **Endpoint:** `POST https://fal.run/fal-ai/trellis`
> **Endpoint ID:** `fal-ai/trellis`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/trellis/playground)
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
>     "fal-ai/trellis",
>     arguments={
>         "image_url": "https://storage.googleapis.com/falserverless/web-examples/rodin3d/warriorwoman.png"
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
> const result = await fal.subscribe("fal-ai/trellis", {
>   input: {
>       image_url: "https://storage.googleapis.com/falserverless/web-examples/rodin3d/warriorwoman.png"
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
>   --url https://fal.run/fal-ai/trellis \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_url": "https://storage.googleapis.com/falserverless/web-examples/rodin3d/warriorwoman.png"
> }'
> ```
>
> ### Input Schema
>
> **image\_url** `string` *required*
>
> URL of the input image to convert to 3D
>
> **seed** `integer`
>
> Random seed for reproducibility
>
> **ss\_guidance\_strength** `float` (default: 7.5)
>
> Guidance strength for sparse structure generation Default value: `7.5`
>
> Range: `0` to `10`
>
> **ss\_sampling\_steps** `integer` (default: 12)
>
> Sampling steps for sparse structure generation Default value: `12`
>
> Range: `1` to `50`
>
> **slat\_guidance\_strength** `float` (default: 3)
>
> Guidance strength for structured latent generation Default value: `3`
>
> Range: `0` to `10`
>
> **slat\_sampling\_steps** `integer` (default: 12)
>
> Sampling steps for structured latent generation Default value: `12`
>
> Range: `1` to `50`
>
> **mesh\_simplify** `float` (default: 0.95)
>
> Mesh simplification factor Default value: `0.95`
>
> Range: `0.9` to `0.98`
>
> **texture\_size** `TextureSizeEnum` (default: 1024)
>
> Texture resolution Default value: `"1024"`
>
> Possible values: `512`, `1024`, `2048`
>
> ### Output Schema
>
> **model\_mesh** `File` *required*
>
> Generated 3D mesh file
>
> **timings** `Timings` *required*
>
> Processing timings
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_url": "https://storage.googleapis.com/falserverless/web-examples/rodin3d/warriorwoman.png",
>   "ss_guidance_strength": 7.5,
>   "ss_sampling_steps": 12,
>   "slat_guidance_strength": 3,
>   "slat_sampling_steps": 12,
>   "mesh_simplify": 0.95,
>   "texture_size": 1024
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "model_mesh": {
>     "url": "",
>     "content_type": "image/png",
>     "file_name": "z9RV14K95DvU.png",
>     "file_size": 4404019
>   }
> }
> ```

> [tab] Multi
>
> **Endpoint:** `POST https://fal.run/fal-ai/trellis/multi`
> **Endpoint ID:** `fal-ai/trellis/multi`
>
> > [card] [Try it in the Playground](https://fal.ai/models/fal-ai/trellis/multi/playground)
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
>     "fal-ai/trellis/multi",
>     arguments={
>         "image_urls": [
>             "https://storage.googleapis.com/falserverless/model_tests/video_models/front.png",
>             "https://storage.googleapis.com/falserverless/model_tests/video_models/back.png",
>             "https://storage.googleapis.com/falserverless/model_tests/video_models/left.png"
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
> const result = await fal.subscribe("fal-ai/trellis/multi", {
>   input: {
>       image_urls: [
>         "https://storage.googleapis.com/falserverless/model_tests/video_models/front.png",
>         "https://storage.googleapis.com/falserverless/model_tests/video_models/back.png",
>         "https://storage.googleapis.com/falserverless/model_tests/video_models/left.png"
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
>   --url https://fal.run/fal-ai/trellis/multi \
>   --header "Authorization: Key $FAL_KEY" \
>   --header "Content-Type: application/json" \
>   --data '{
>   "image_urls": [
>     "https://storage.googleapis.com/falserverless/model_tests/video_models/front.png",
>     "https://storage.googleapis.com/falserverless/model_tests/video_models/back.png",
>     "https://storage.googleapis.com/falserverless/model_tests/video_models/left.png"
>   ]
> }'
> ```
>
> ### Input Schema
>
> **image\_urls** `list\<string\>` *required*
>
> List of URLs of input images to convert to 3D
>
> **seed** `integer`
>
> Random seed for reproducibility
>
> **ss\_guidance\_strength** `float` (default: 7.5)
>
> Guidance strength for sparse structure generation Default value: `7.5`
>
> Range: `0` to `10`
>
> **ss\_sampling\_steps** `integer` (default: 12)
>
> Sampling steps for sparse structure generation Default value: `12`
>
> Range: `1` to `50`
>
> **slat\_guidance\_strength** `float` (default: 3)
>
> Guidance strength for structured latent generation Default value: `3`
>
> Range: `0` to `10`
>
> **slat\_sampling\_steps** `integer` (default: 12)
>
> Sampling steps for structured latent generation Default value: `12`
>
> Range: `1` to `50`
>
> **mesh\_simplify** `float` (default: 0.95)
>
> Mesh simplification factor Default value: `0.95`
>
> Range: `0.9` to `0.98`
>
> **texture\_size** `TextureSizeEnum` (default: 1024)
>
> Texture resolution Default value: `"1024"`
>
> Possible values: `512`, `1024`, `2048`
>
> **multiimage\_algo** `MultiimageAlgoEnum` (default: stochastic)
>
> Algorithm for multi-image generation Default value: `"stochastic"`
>
> Possible values: `stochastic`, `multidiffusion`
>
> ### Output Schema
>
> **model\_mesh** `File` *required*
>
> Generated 3D mesh file
>
> **timings** `Timings` *required*
>
> Processing timings
>
> ### Input Example
>
> ```json theme={null}
> {
>   "image_urls": [
>     "https://storage.googleapis.com/falserverless/model_tests/video_models/front.png",
>     "https://storage.googleapis.com/falserverless/model_tests/video_models/back.png",
>     "https://storage.googleapis.com/falserverless/model_tests/video_models/left.png"
>   ],
>   "ss_guidance_strength": 7.5,
>   "ss_sampling_steps": 12,
>   "slat_guidance_strength": 3,
>   "slat_sampling_steps": 12,
>   "mesh_simplify": 0.95,
>   "texture_size": 1024,
>   "multiimage_algo": "stochastic"
> }
> ```
>
> ### Output Example
>
> ```json theme={null}
> {
>   "model_mesh": {
>     "url": "",
>     "content_type": "image/png",
>     "file_name": "z9RV14K95DvU.png",
>     "file_size": 4404019
>   }
> }
> ```

## Trellis - Advanced AI Model for 3D Generation

Transform your creative workflows with Trellis, a powerful AI model designed for reliable, high-quality 3D asset generation from images. Built for developers who need production-ready 3D models with minimal complexity.

### Overview

Trellis provides a streamlined API for generating 3D models from images, utilizing a native 3D generative model based on Structured LATents (SLAT) representation. Whether you're building creative tools, content generation systems, or enhancing existing applications, Trellis delivers consistent, high-quality 3D assets with simple integration.

### Getting Started

Setting up Trellis takes just a few minutes. Choose your preferred SDK:

For JavaScript/TypeScript projects:

```bash theme={null}
npm install --save @fal-ai/client
```

For Python applications:

```bash theme={null}
pip install fal-client
```

Configure your authentication:

JavaScript:

```javascript theme={null}
import { fal } from "@fal-ai/client";

fal.config({
  credentials: "YOUR_FAL_KEY_HERE"
});
```

Python:

```python theme={null}
import fal_client
import os

os.environ["FAL_KEY"] = "YOUR_FAL_KEY_HERE"
```

### Basic Usage

Generate your first 3D model with just a few lines of code:

JavaScript:

```javascript theme={null}
const result = await fal.subscribe("fal-ai/trellis", {
  input: {
    image_url: "https://example.com/your-image.jpg"
  }
});
```

Python:

```python theme={null}
result = fal_client.subscribe("fal-ai/trellis", {
    "image_url": "https://example.com/your-image.jpg"
})
```

### Advanced Features

Trellis supports advanced configuration options to fine-tune your results:

**3D Output Formats**

* Generate multiple 3D representations (Radiance Fields, 3D Gaussians, meshes)
* Export to GLB format for universal compatibility
* Maintain high detail in both geometry and texture

**Quality Control**

* Intricate shape and texture detail preservation
* Flexible output format selection
* Support for local 3D editing capabilities

### API Parameters

**Input Parameters:**

* `image_url` (required): URL of the input image
* Additional configuration options available for advanced use cases

**Output:**

* 3D model in multiple formats
* GLB file export capability
* High-quality mesh and texture data

### Best Practices

Maximize your success with Trellis by following these guidelines:

**Error Handling**

```javascript theme={null}
try {
  const result = await fal.subscribe("fal-ai/trellis", {
    input: { image_url: "your-image-url" }
  });
} catch (error) {
  console.error("Generation failed:", error.message);
}
```

**Image Requirements**

* Use clear, well-lit images with distinct subjects
* Supported formats: JPG, JPEG, PNG, WebP, GIF, AVIF
* Best results with images showing full object visibility

### Technical Specifications

**Model Architecture:**

* Based on Structured LATents (SLAT) representation
* Powered by Rectified Flow Transformers
* Up to 2 billion parameters
* Trained on 500K diverse 3D objects

**Performance Metrics:**

* Fast 3D generation from single images
* High-quality output with intricate details
* Multiple output format support

### File Upload Support

Upload local images using the file storage API:

```javascript theme={null}
import { fal } from "@fal-ai/client";

const file = new File([imageData], "image.jpg", { type: "image/jpeg" });
const url = await fal.storage.upload(file);

const result = await fal.subscribe("fal-ai/trellis", {
  input: {
    image_url: url
  }
});
```

### Queue-Based Processing

For asynchronous workflows:

```javascript theme={null}
// Submit request
const { request_id } = await fal.queue.submit("fal-ai/trellis", {
  input: {
    image_url: "your-image-url"
  },
  webhookUrl: "https://optional.webhook.url/for/results"
});

// Check status
const status = await fal.queue.status("fal-ai/trellis", {
  requestId: request_id,
  logs: true
});

// Get result
const result = await fal.queue.result("fal-ai/trellis", {
  requestId: request_id
});
```

### Integration Support

Our documentation includes complete reference implementations for:

* React/Next.js applications
* Python backend services
* Node.js environments
* REST API clients

### Use Cases

**3D Asset Creation**

* Game development assets
* Product visualization
* Digital art and design
* AR/VR content creation

**Professional Applications**

* E-commerce product models
* Architecture visualization
* Educational content
* Creative prototyping

### Pricing and Usage

* **Cost**: \$0.02 per unit
* Transparent, usage-based pricing
* No subscription necessary
* No hidden fees or minimum commitments

[View detailed pricing](https://fal.ai/pricing) or [contact sales](mailto:support@fal.ai) for enterprise solutions.

### Support and Resources

Access comprehensive support through multiple channels:

**Documentation**

* [API Reference](https://fal.ai/models/fal-ai/trellis/api)
* [General fal.ai Documentation](https://docs.fal.ai/)
* Code examples and tutorials

**Community Support**

* Active developer community
* Regular updates and improvements
* Direct technical support

Get started with Trellis today and experience the next generation of AI-powered 3D asset generation. Sign up for an API key at [fal.ai](https://fal.ai/login).

## Related

* [Trellis](trellis.md) — 3D

## Limitations

* `ss_guidance_strength` range: 0 to 10
* `ss_sampling_steps` range: 1 to 50
* `slat_guidance_strength` range: 0 to 10
* `slat_sampling_steps` range: 1 to 50
* `mesh_simplify` range: 0.9 to 0.98
* `texture_size` restricted to: `512`, `1024`, `2048`
* `multiimage_algo` restricted to: `stochastic`, `multidiffusion`
