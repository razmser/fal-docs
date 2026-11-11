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

# Introduction to Serverless

> Deploy custom AI models on GPU infrastructure that autoscales from zero to thousands of machines.

fal Serverless lets you deploy your own AI models, pipelines, and applications on [serverless GPUs](https://fal.ai/serverless) that scale automatically. You write a Python class, define your hardware requirements, and fal handles provisioning, scaling, networking, and observability. Your code runs on H100s, A100s, or any other [machine type](deployment/machine-types.md) you choose, scaling from zero runners to thousands based on demand, and back to zero when traffic stops.

Every model in the [Model APIs](model-apis/overview.md) marketplace is a `fal.App` running on Serverless. When you deploy your own app, you get the same [queue-based reliability](model-apis/inference/queue.md), the same [analytics dashboard](serverless/observability/app-analytics.md), and the same [client SDKs](development/calling-your-endpoints.md). The difference is that you control the code, the model weights, and the container environment. You can also [publish your app to the marketplace](serverless/publishing-to-marketplace.md) so anyone can call it with their own API key. For an overview of how Serverless and Model APIs work together, see the [fal inference platform](https://fal.ai/inference).

> [!NOTE]
>
> **Serverless access is approved per account.** A fal account is free. To deploy your own apps on fal Serverless, the fal team must approve access for your account. Request access on the [Serverless Get Started page](https://fal.ai/dashboard/serverless-get-started). You do not need this approval to call [Model APIs](model-apis/overview.md).

## How It Works

The best deployment approach depends on where you are starting from. If you are migrating from another provider, you can be up and running with minimal code changes. If you are starting a new project, fal can build and manage the container for you. All three paths give you the same autoscaling, observability, and [runner](https://fal.ai/docs/documentation/getting-started/runners-and-caching) management.

### Migrating an existing server

If you already have a working HTTP server (FastAPI, Flask, or any framework), this is the fastest path. Use Direct Server Mode with `exposed_port`, and fal routes traffic to your server's port with no code changes to your existing application.

```toml theme={null}
[tool.fal.apps.my-server]
auth = "private"
machine_type = "GPU-A100"
exposed_port = 8000

[tool.fal.apps.my-server.image]
dockerfile = "Dockerfile"
```

Direct Server Mode supports the same scaling parameters as `fal.App` (keep\_alive, min\_concurrency, max\_concurrency, and more). See [Migrate a Docker Server](development/migrate-external-docker-server.md) for a complete walkthrough and the [pyproject.toml reference](../api-reference/python-sdk/pyproject-toml.md) for the full configuration schema. There are also step-by-step guides for [Replicate](development/migrate-from-replicate.md), [Modal](development/migrate-from-modal.md), and [RunPod](development/migrate-from-runpod.md).

### Migrating a custom container

If you have a Docker image with your model and dependencies baked in but not a full HTTP server, you can bring it directly. Use `ContainerImage` to reference your Dockerfile or pull from a registry. You keep full control over the build while using fal's endpoint system and scaling.

```python theme={null}
import fal
from fal.container import ContainerImage

class MyModel(fal.App):
    image = ContainerImage.from_dockerfile_str("""
        FROM pytorch/pytorch:2.1.0-cuda12.1-cudnn8-runtime
        RUN apt-get update && apt-get install -y ffmpeg curl
        RUN pip install diffusers transformers
        RUN pip install fal
    """)
    machine_type = "GPU-A100"

    def setup(self):
        from diffusers import StableDiffusionXLPipeline
        self.pipe = StableDiffusionXLPipeline.from_pretrained(
            "stabilityai/stable-diffusion-xl-base-1.0"
        ).to("cuda")

    @fal.endpoint("/")
    def generate(self, input: dict) -> dict:
        image = self.pipe(input["prompt"]).images[0]
        return {"image": image}
```

You can pull from private registries (Docker Hub, GCP Artifact Registry, AWS ECR, Azure Container Registry). See [Custom Container Images](development/use-custom-container-image.md) for the full guide.

### Starting a new project

If you are building from scratch, use a native `fal.App` with pip requirements. You write a Python class, list your dependencies, and define your endpoints. fal builds the container for you.

```python theme={null}
import fal
from pydantic import BaseModel

class Input(BaseModel):
    prompt: str

class Output(BaseModel):
    result: str

class MyModel(fal.App):
    machine_type = "GPU-H100"
    requirements = ["torch", "transformers"]

    def setup(self):
        from transformers import pipeline
        self.pipe = pipeline("text-generation", model="gpt2", device="cuda")

    @fal.endpoint("/")
    def generate(self, input: Input) -> Output:
        result = self.pipe(input.prompt, max_length=50)[0]["generated_text"]
        return Output(result=result)
```

For the full environment setup options, see [Defining Your Environment](development/container-setup.md).

### Test and deploy

Regardless of which approach you use, the workflow is the same: **validate with `fal run` first, then `fal deploy`.** `fal run` starts your app on a temporary worker and gives you a throwaway URL. It runs `setup()` and your endpoints exactly as production will, so you catch import errors, missing dependencies, and model-loading failures before they become a production crashloop. Once `fal run` works, `fal deploy` promotes the same app to a persistent URL and automatic scaling based on incoming traffic. Test the deployed app from **Testing > Playground** in its dashboard. See the [Playground guide](model-apis/playground.md#your-own-apps) for the complete flow and the [Quick Start](development/getting-started/quick-start.md) to try it in under two minutes.

## Scaling, Observability, and Cost

Once your app is deployed, fal manages the infrastructure for you. [Runners](deployment/runners.md) spin up when requests arrive and shut down when idle. You control the tradeoff between latency and cost with parameters like [keep\_alive](deployment/scale-your-application.md) (how long idle runners stay warm) and [min\_concurrency](deployment/scale-your-application.md) (minimum warm runners). To understand how runners transition between states and how caching reduces [cold starts](serverless/optimizations/optimize-cold-starts.md), see [Runners and Caching](https://fal.ai/docs/documentation/getting-started/runners-and-caching).

Observability is built in. [App Analytics](serverless/observability/app-analytics.md) shows request volume, latency percentiles, runner utilization, and startup duration. [Error Analytics](serverless/observability/error-analytics.md) surfaces failing requests with stack traces. You can export metrics to your own stack via the [Prometheus-compatible API](serverless/observability/exporting-metrics.md) or forward logs with [Log Drains](serverless/observability/log-drains.md). For billing, see [Serverless Pricing](serverless/pricing.md) and [Optimizing Costs](serverless/optimizations/optimizing-costs.md).

## Next Steps

If you are new to fal, start with the [Quick Start](development/getting-started/quick-start.md), which walks through building and deploying a Hello World app in under two minutes. The [Deploy Your First Image Generator](development/getting-started/deploy-your-first-image-generator.md) tutorial applies the same workflow to a real Stable Diffusion XL model. Once you are comfortable with the basics, the [App Lifecycle](development/app-lifecycle.md) page explains how apps are structured, where code runs, and how runners start up and shut down.

For more deployment examples covering text-to-image, video, speech, ComfyUI, and custom containers, browse the [Examples](../examples.md#deploying-models) section. For recent platform updates, check the [Changelog](../changelog.md) or watch the overview below.

From Alert to Root Cause

June 2026

Scaling, observability, cold starts & multi-GPU

January 2026
