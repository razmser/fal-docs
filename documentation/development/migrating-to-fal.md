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

# Migrating to fal

> Bring your existing models and infrastructure to fal with minimal code changes.

If you are already running AI models on another platform or on your own infrastructure, fal is designed to make migration straightforward. You can bring your existing Docker containers, HTTP servers, or platform-specific code and deploy them on fal's GPU infrastructure with minimal changes. The guides in this section provide step-by-step walkthroughs for common migration paths.

If you are starting a new project rather than migrating, you can skip this section entirely and go to [Defining Your Environment](container-setup.md) to set up your app from scratch.

The fastest path is [Migrate a Docker Server](migrate-external-docker-server.md), which lets you deploy any existing HTTP server with a Dockerfile and `exposed_port` in `pyproject.toml`, with no changes to your application code. If you are coming from a specific platform, the [Replicate](migrate-from-replicate.md), [Modal](migrate-from-modal.md), [RunPod](migrate-from-runpod.md), and [Baseten](migrate-from-baseten.md) guides provide side-by-side code comparisons and cover how to map your existing configuration (GPU types, scaling, secrets, storage) to fal equivalents.
