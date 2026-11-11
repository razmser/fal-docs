> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Environment and Runtime

> Choose how to define the environment your fal app runs in.

Before your app can run on fal, the platform needs to know what packages, system libraries, and runtime your code depends on. This page helps you choose between two approaches: using fal's managed Python runtime with a pip requirements list, or bringing your own Docker container for full control over the environment.

If your app only needs pip packages, the managed runtime is the simplest option. You list your dependencies in the `requirements` attribute and fal handles CUDA setup and environment isolation, so you do not need to write or maintain a Dockerfile. If you need system-level packages (like `ffmpeg`), a specific CUDA version, a non-Python runtime, or you are migrating an existing Docker-based server, the custom container path gives you full control over the build. Both approaches produce the same kind of [runner](https://fal.ai/docs/documentation/getting-started/runners-and-caching) and support all the same scaling, observability, and deployment features.

## fal Runtime

For apps that only need pip packages, the managed runtime is the simplest setup. You define your pip dependencies in the `requirements` attribute of your app class and fal builds the container for you. This approach handles CUDA and PyTorch setup automatically based on your chosen [machine type](../deployment/machine-types.md), so you do not need to manage base images or CUDA library versions.

```python theme={null}
class MyApp(fal.App):
    machine_type = "GPU-A100"
    requirements = ["torch==2.4.0", "diffusers", "transformers"]
```

If your project has local Python modules or external repositories you need to include, the [Import Code](import-code.md) page covers `app_files`, `local_python_modules`, and git clone patterns.

See [fal Runtime](fal-runtime.md) for the full guide including CUDA handling, extra index URLs, and best practices.

## Custom Container

When you need full control over the environment, use a Dockerfile. This is the right choice when your app requires system packages, a specific base image, non-Python dependencies, or you are bringing an existing Docker image from another platform.

```python theme={null}
from fal.container import ContainerImage

class MyApp(fal.App):
    image = ContainerImage.from_dockerfile_str("""
        FROM pytorch/pytorch:2.1.0-cuda12.1-cudnn8-runtime
        RUN apt-get update && apt-get install -y ffmpeg
        RUN pip install diffusers transformers
    """)
```

You can also pull from private registries (Docker Hub, Google Artifact Registry, AWS ECR, Azure Container Registry) with authentication. See [Custom Container Images](use-custom-container-image.md) for the full Dockerfile reference and [Private Registries](private-registries.md) for registry authentication setup.

## Which Should I Use?

| Situation | Recommended |
| - | - |
| New Python model with pip packages | [fal Runtime](fal-runtime.md) |
| Need system packages (ffmpeg, imagemagick) | [Custom Container](use-custom-container-image.md) |
| Specific CUDA or PyTorch version | [Custom Container](use-custom-container-image.md) |
| Migrating an existing Docker server | [Custom Container + exposed\_port](migrate-external-docker-server.md) |
| Non-Python runtime (Node.js, Go, etc.) | [Custom Container](use-custom-container-image.md) |
| Local modules or git repositories | [fal Runtime + Import Code](import-code.md) |
