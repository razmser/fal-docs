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

# Why fal?

> Industry-leading inference speed for generative AI, trusted by top AI applications

## Model APIs

**Unified API, 1,000+ models.** One API to access every model on fal. Switch between FLUX, Nano Banana 2, Kling 3.0, Sora 2, Whisper, and more with a single parameter change. No need to integrate separate providers or manage different SDKs. fal moves fast on new model launches too, with day-0 availability the moment a model drops, so you're never waiting to integrate the latest generation.

```python theme={null}
import fal_client

# Switch models by changing one string
result = fal_client.subscribe("fal-ai/flux/schnell", arguments={"prompt": "a sunset over mountains"})
result = fal_client.subscribe("fal-ai/nano-banana-2", arguments={"prompt": "a sunset over mountains"})
```

**Fastest inference for generative media.** Speed is where fal started and where it continues to invest the most. Proprietary optimizations, including custom CUDA kernels, optimized attention layers, and inference engine tuning, consistently deliver the fastest inference for diffusion and generative media workloads. Reliability matches the speed: 99.99% historical uptime, fault tolerance, and intelligent request queuing are built in.

**Test and compare before you commit.** [Sandbox](https://fal.ai/sandbox) lets you run the same prompt across multiple models side by side, estimate costs before running, and share results with your team. Instead of guessing which model fits your use case, you can see the differences visually and make model selection concrete.

***

## Serverless

**Deploy your own models on battle-tested infrastructure.** Every endpoint on the [fal Marketplace](https://fal.ai/models) runs on [fal Serverless](serverless.md), the same infrastructure you deploy your own models on. fal has been running this system for over 3 years, serving tens of millions of requests daily. If fal Serverless breaks, fal's own products break. That alignment means the engine your code runs on is constantly being optimized.

**Scale automatically, or reserve capacity.** fal scales from zero to thousands of GPUs across H100s, H200s, A100s, and more. For teams that need guaranteed capacity, fal also offers reserved GPU allocations.

**Dedicated engineering support.** Every customer gets a dedicated Slack channel with engineers across timezones. For enterprise contracts, fal's ML specialists work directly with your team, optimizing your code, tuning cold starts, and iterating from development through production. Migration is straightforward too: bring your existing [container images](development/use-custom-container-image.md), use the [quickstart](development/getting-started/quick-start.md) to deploy in minutes, or connect to the [MCP](https://fal.ai/docs/documentation/serverless/mcp) and migrate in one shot.

***

> [card] [Model APIs](model-apis/overview.md)
>
> Start calling 1,000+ models with a single API

> [card] [Serverless](serverless.md)
>
> Deploy your own models on fal's infrastructure
