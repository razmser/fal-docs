> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../quickstart.md) and the [Model APIs overview](../../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Deploy Your First Image Generator

> Deploy a text-to-image AI model in under 5 minutes. This tutorial walks you through creating your own image generation API using Stable Diffusion XL.

This tutorial builds on the [Quick Start](quick-start.md) by deploying a real AI model. You will create a text-to-image API powered by Stable Diffusion XL that runs on GPU infrastructure, loads model weights from Hugging Face, and returns generated images through a typed Pydantic schema. The result is a production-ready endpoint with a [Playground](../../model-apis/playground.md#your-own-apps) for browser-based testing.

Along the way, you will encounter several concepts that are central to building Serverless apps: the [setup()](../app-lifecycle.md) hook for one-time model loading, the [keep\_alive](../../deployment/scale-your-application.md) parameter for controlling runner lifetime, pip [requirements](../fal-runtime.md) for environment setup, and the [fal.toolkit.Image](../handle-inputs-and-outputs.md#image-output) class for returning media outputs. If any of these are new to you, the [Serverless Introduction](../../serverless.md) provides a quick overview.

## Before You Start

You'll need:

* Python - we recommend 3.11
* A [fal account](https://fal.ai/dashboard) (sign up is free)
* Basic familiarity with Python and AI models

## Step 1: Install the CLI

If you haven't already:

```bash theme={null}
pip install fal
```

## Step 2: Authenticate

Get your API key from the [fal dashboard](https://fal.ai/dashboard) and authenticate:

```bash theme={null}
fal auth login
```

This opens your browser to authenticate with fal. Once complete, your credentials are saved locally.

## Step 3: Create Your Image Generator

Create a file called `image_generator.py` with this Stable Diffusion XL text-to-image model:

```python theme={null}
import fal
from pydantic import BaseModel, Field
from fal.toolkit import Image

class Input(BaseModel):
    prompt: str = Field(
        description="The prompt to generate an image from",
        examples=["A beautiful image of a cat"],
    )

class Output(BaseModel):
    image: Image

class MyApp(fal.App):
    keep_alive = 300
    app_name = "my-demo-app"
    machine_type = "GPU-H100"
    requirements = [
        "hf-transfer==0.1.9",
        "diffusers[torch]==0.32.2",
        "transformers[sentencepiece]==4.51.0",
        "accelerate==1.6.0",
    ]

    def setup(self):
        # Enable HF Transfer for faster downloads
        import os

        os.environ["HF_HUB_ENABLE_HF_TRANSFER"] = "1"

        import torch
        from diffusers import StableDiffusionXLPipeline

        # Load any model you want, we'll use stabilityai/stable-diffusion-xl-base-1.0
        # Huggingface models will be automatically downloaded to
        # the persistent storage of your account (/data)
        self.pipe = StableDiffusionXLPipeline.from_pretrained(
            "stabilityai/stable-diffusion-xl-base-1.0",
            torch_dtype=torch.float16,
            variant="fp16",
            use_safetensors=True,
        ).to("cuda")

        # Warmup the model before the first request
        self.warmup()

    def warmup(self):
        self.pipe("A beautiful image of a cat")

    @fal.endpoint("/")
    def run(self, request: Input) -> Output:
        result = self.pipe(request.prompt)
        image = Image.from_pil(result.images[0])
        return Output(image=image)
```

## Step 4: Test Your Image Generator

Run your model to test it:

```bash theme={null}
fal run image_generator.py::MyApp
```

This starts your app, downloads the Stable Diffusion XL model weights (cached after the first run), and prints two URLs: a direct HTTP endpoint and a web playground. The first run takes a couple of minutes for the model download. Subsequent runs are faster.

Once you see `Application startup complete`, test it with curl using the URL from the output:

```bash theme={null}
curl $FAL_RUN_URL -H 'Content-Type: application/json' \
  -d '{"prompt":"A cyberpunk cityscape at night with neon lights"}'
```

You can also use the playground URL to test through a browser interface.

## Step 5: Deploy Your Model

Once you are satisfied with the results, deploy your app to create a persistent URL. Deployed apps scale automatically, with [runners](../../deployment/runners.md) managed by fal's infrastructure. You can configure scaling behavior through parameters like `keep_alive`, `min_concurrency`, and `max_concurrency`. See [Scale Your Application](../../deployment/scale-your-application.md) to learn more.

```bash theme={null}
fal deploy image_generator.py::MyApp
```

Open the persistent app in the [Apps dashboard](https://fal.ai/dashboard/apps). Follow [Testing Your Own Apps](../../model-apis/playground.md#your-own-apps) to test its endpoint. The Playground URL from `fal run` in Step 4 remains tied to that temporary run.

## Step 6: Call Your Deployed App

Once deployed, call your image generator from any Python or JavaScript application using the fal client SDK.

> [tab] Python
>
> ```bash theme={null}
> pip install fal-client
> ```
>
> ```python theme={null}
> import fal_client
>
> result = fal_client.subscribe(
>     "your-username/my-demo-app",
>     arguments={"prompt": "A cyberpunk cityscape at night with neon lights"},
> )
> print(result["image"]["url"])
> ```

> [tab] JavaScript
>
> ```bash theme={null}
> npm install @fal-ai/client
> ```
>
> ```javascript theme={null}
> import { fal } from "@fal-ai/client";
>
> const result = await fal.subscribe("your-username/my-demo-app", {
>   input: { prompt: "A cyberpunk cityscape at night with neon lights" },
> });
> console.log(result.data.image.url);
> ```

Replace `your-username/my-demo-app` with the endpoint ID shown after deploying. See [Calling Your Endpoints](../calling-your-endpoints.md) for all calling patterns including async queue, streaming, real-time, and webhooks.

## Next Steps

The [App Lifecycle](../app-lifecycle.md) page explains how apps are structured, where code runs, and how runners start up and shut down. To define richer input and output schemas (sliders, image uploads, multiple outputs), see [Handle Inputs and Outputs](../handle-inputs-and-outputs.md). For all the ways to call your deployed app from client code, including async queue, streaming, and real-time patterns, see [Calling Your Endpoints](../calling-your-endpoints.md).
