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

# Application Setup

> Prepare your app's runtime environment: load models, download weights, and configure persistent storage.

Once you have defined your [environment](container-setup.md) (pip requirements or a Docker container) and written your [app class](app-lifecycle.md), the next step is preparing what happens when a runner starts. This section covers the resources your app needs at runtime: where to store model weights, how to download them efficiently, and how to structure your [setup()](app-lifecycle.md#runner-startup) method so that cold starts are as fast as possible.

Everything in `setup()` runs once per [runner](../deployment/runners.md), not once per request. This is where you load model weights into GPU memory, initialize connections, and prepare any state that your endpoint methods will use. Objects you attach to `self` (like `self.model`) persist for the lifetime of the runner, so expensive operations only happen at startup. The trade-off is that everything in `setup()` contributes to your app's cold start time, which is the delay a request experiences when no idle runner is available and a new one must be provisioned.

```python theme={null}
class MyModel(fal.App):
    machine_type = "GPU-A100"
    requirements = ["torch", "diffusers"]

    def setup(self):
        import torch
        from diffusers import StableDiffusionXLPipeline

        self.pipe = StableDiffusionXLPipeline.from_pretrained(
            "stabilityai/stable-diffusion-xl-base-1.0",
            torch_dtype=torch.float16,
        ).to("cuda")

        self.pipe("warmup")

    @fal.endpoint("/")
    def generate(self, input: dict) -> dict:
        image = self.pipe(input["prompt"]).images[0]
        return {"image": image}
```

In this example, `setup()` downloads SDXL weights (cached to `/data` automatically for HuggingFace models), loads them into GPU memory, and runs a warmup inference to compile any lazy kernels. All of this happens once. Every subsequent request reuses `self.pipe` without any initialization overhead.

## What to Read Next

The pages in this section cover the infrastructure that supports your setup method. [Persistent Storage](use-persistent-storage.md) explains the `/data` volume where model weights and datasets are cached across runners, including how the distributed filesystem works, how to upload files outside of your app, and how to handle concurrent writes safely. [Downloading Models and Files](download-model-weights-and-files.md) covers the `download_file()` and `download_model_weights()` toolkit utilities, along with detailed Hugging Face optimization techniques for faster initial downloads, parallel file pre-reading, and compiled kernel caching.

For strategies to reduce cold start time beyond what is covered here, see [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md), which covers container image optimization, scaling parameters, and [FlashPack](../serverless/optimizations/flashpack.md) for high-throughput tensor loading.
