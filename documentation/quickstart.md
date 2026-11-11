> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](quickstart.md) and the [Model APIs overview](model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Quick Start: Get an API Key and Call a Model

> Create an API key in the fal dashboard, set FAL_KEY, and generate your first image with Python, JavaScript, or cURL. Or deploy your own model on fal.

fal gives you two ways to work with AI models. If you want to generate images, video, audio, or other media, the [Model APIs](model-apis/overview.md) let you call 1,000+ production-ready models with a single API call. If you have your own model to deploy, [Serverless](serverless.md) gives you the full lifecycle: develop, test, deploy, and scale on the same infrastructure that powers the [marketplace](https://fal.ai/models).

Both paths start with an [API key](https://fal.ai/docs/documentation/model-apis/authentication.md) and take a few minutes. The "consume" path is for calling existing models through fal's [client libraries](model-apis/inference/client-setup.md) or HTTP. The "deploy" path is for teams bringing their own models to run on fal's GPU infrastructure, using the same [fal.App](development/app-setup.md) framework that powers every model on the platform.

> [tab] I want to consume a model
>
> ### What do you want to build?
>
> > [card] [Generate Images](../examples/image-generation/generate-images-from-text.md)
> >
> > Create images from text prompts with FLUX, Nano Banana 2, and more
>
> > [card] [Generate Videos](../examples/video-generation/generate-videos-from-image.md)
> >
> > Transform images into videos with Kling 3.0, Sora 2, and other models
>
> > [card] [Transcribe Audio](../examples/audio-speech/convert-speech-to-text.md)
> >
> > Convert speech to text with Whisper
>
> > [card] [Use LLMs](../examples/integrations/use-llms.md)
> >
> > Build with Llama, Mistral, and other large language models
>
> > [card] [Fast FLUX](../examples/image-generation/fast-flux.md)
> >
> > Ultra-fast image generation with optimized FLUX
>
> > [card] [Build a Workflow UI](../examples/integrations/custom-workflow-ui.md)
> >
> > Create interfaces for complex AI workflows
>
> > [card] [Next.js Integration](../examples/integrations/nextjs.md)
> >
> > Build full-stack AI apps with Next.js and fal
>
> > [card] [Vercel Integration](../examples/integrations/vercel.md)
> >
> > Deploy AI-powered apps on Vercel with fal
>
> > [card] [n8n Integration](../examples/integrations/n8n.md)
> >
> > Automate workflows by connecting fal models to n8n
>
> ### Quick example
>
> Generate your first image in under a minute.
>
> **1. Install the client**
>
> ```bash Python theme={null}
> pip install fal-client
> ```
>
> ```bash JavaScript theme={null}
> npm install @fal-ai/client
> ```
>
> **2. Set your API key**
>
> Get a key from the [fal dashboard](https://fal.ai/dashboard/keys) and set it as an environment variable:
>
> ```bash theme={null}
> export FAL_KEY="your-api-key-here"
> ```
>
> **3. Generate an image**
>
> ```python Python theme={null}
> import fal_client
>
> result = fal_client.subscribe("fal-ai/flux/schnell", arguments={
>     "prompt": "a futuristic cityscape at sunset"
> })
> print(result["images"][0]["url"])
> ```
>
> ```javascript JavaScript theme={null}
> import { fal } from "@fal-ai/client";
>
> const result = await fal.subscribe("fal-ai/flux/schnell", {
>   input: { prompt: "a futuristic cityscape at sunset" }
> });
> console.log(result.data.images[0].url);
> ```
>
> ```bash cURL theme={null}
> curl -X POST "https://queue.fal.run/fal-ai/flux/schnell" \
>   -H "Authorization: Key $FAL_KEY" \
>   -H "Content-Type: application/json" \
>   -d '{"prompt": "a futuristic cityscape at sunset"}'
> ```

> [tab] I want to deploy a model
>
> ### What do you want to deploy?
>
> > [card] [Text-to-Image Model](../examples/image-generation/deploy-text-to-image-model.md)
> >
> > Deploy Sana, FLUX, or your custom image generation model
>
> > [card] [Text-to-Video Model](../examples/video-generation/deploy-text-to-video-model.md)
> >
> > Deploy WAN or other video generation models
>
> > [card] [Text-to-Speech Model](../examples/audio-speech/deploy-text-to-speech-model.md)
> >
> > Deploy Kokoro or custom voice synthesis models
>
> > [card] [Text-to-Music Model](../examples/audio-speech/deploy-text-to-music-model.md)
> >
> > Deploy DiffRhythm or music generation models
>
> > [card] [ComfyUI Server](../examples/image-generation/deploy-comfyui-server.md)
> >
> > Run ComfyUI workflows as a serverless API
>
> > [card] [LoRA Training](../examples/image-generation/deploy-wan-lora-training.md)
> >
> > Fine-tune WAN video generation with LoRA training
>
> > [card] [Multi-GPU Inference](../examples/video-generation/deploy-multi-gpu-inference.md)
> >
> > Scale generation across multiple GPUs with streaming
>
> > [card] [3D Progressive Rendering](../examples/video-generation/deploy-3d-progressive-rendering.md)
> >
> > Stream real-time text-to-3D reconstruction
>
> > [card] [Real-time Video Style Transfer](../examples/video-generation/deploy-realtime-video-restyle.md)
> >
> > Restyle a live webcam stream with a text prompt
>
> > [card] [Real-time World Model](../examples/video-generation/deploy-realtime-world-model.md)
> >
> > Deploy a real-time world model powered by Matrix-Game
>
> > [card] [Custom Container](../examples/deploy-models-with-custom-containers.md)
> >
> > Deploy any model with custom Docker containers
>
> ### Migrating from another platform?
>
> > [card] [Migrate from Replicate](development/migrate-from-replicate.md)
> >
> > Move your Replicate models to fal
>
> > [card] [Migrate from Modal](development/migrate-from-modal.md)
> >
> > Move your Modal apps to fal
>
> > [card] [Migrate from RunPod](development/migrate-from-runpod.md)
> >
> > Move your RunPod Serverless workers to fal
>
> > [card] [Migrate Docker Server](development/migrate-external-docker-server.md)
> >
> > Move your existing Docker-based inference server to fal
>
> ### Quick example
>
> Deploy your first app in under 2 minutes.
>
> **1. Install the CLI and authenticate**
>
> ```bash theme={null}
> pip install fal
> fal auth login
> ```
>
> **2. Create your app**
>
> Create a file called `my_app.py`:
>
> ```python my_app.py theme={null}
> import fal
> from pydantic import BaseModel
>
> class Input(BaseModel):
>     prompt: str
>
> class Output(BaseModel):
>     message: str
>
> class MyApp(fal.App):
>     @fal.endpoint("/")
>     def run(self, input: Input) -> Output:
>         return Output(message=f"Hello from fal! You said: {input.prompt}")
> ```
>
> > [!NOTE]
> >
> > Declare your endpoint input as a Pydantic model. A bare scalar parameter such as
> > `def run(self, prompt: str)` is interpreted as a **query** parameter, so callers
> > sending a JSON body — which is what the clients and every example here do — get
> > an HTTP 422 response.
>
> **3. Validate with fal run**
>
> Always run this before deploying. `fal run` boots your app on a temporary worker — running `setup()` and your endpoints just like production — so errors surface here instead of as a production crashloop.
>
> ```bash theme={null}
> fal run my_app.py::MyApp
> ```
>
> **4. Deploy to production**
>
> Once `fal run` works, promote the same app to a persistent URL:
>
> ```bash theme={null}
> fal deploy my_app.py::MyApp
> ```
>
> > [card] [Deploy a real model](development/getting-started/deploy-your-first-image-generator.md)
> >
> > Step-by-step guide to deploying an image generation model

***

## Next Steps

> [card] [Get Your API Key](https://fal.ai/docs/documentation/model-apis/authentication.md)
>
> Create an API key to authenticate your requests

> [card] [AI Tools](https://fal.ai/docs/documentation/model-apis/mcp.md)
>
> Use AI coding assistants to build with fal faster

> [card] [Explore Models](https://fal.ai/models)
>
> Browse 1,000+ available models
