> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../documentation/quickstart.md) and the [Model APIs overview](../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Introduction to Platform APIs

> Platform APIs provide programmatic access to platform resources including model metadata, usage tracking, analytics, and fal Assets.

> [card] [Platform APIs for Models](platform-apis/for-models.md)
>
> Access model metadata, pricing information, usage tracking, and analytics for Model APIs

> [card] [Platform APIs for Assets](platform-apis/for-assets.md)
>
> Browse, search, upload, and organize generated or uploaded media in fal Assets

> [card] [Platform APIs for Storage](platform-apis/for-storage.md)
>
> Control access, sign URLs, and manage lifecycle settings for fal CDN files

> [card] [Platform APIs for Serverless](platform-apis/for-serverless.md)
>
> APIs to manage and monitor your serverless applications

> [card] [Platform APIs for Compute](platform-apis/for-compute.md)
>
> Manage compute instances with lifecycle control and monitoring capabilities

> [card] [Platform APIs for Workflows](platform-apis/for-workflows.md)
>
> List and retrieve workflow metadata and definitions

> [card] [Platform APIs for Accounts](platform-apis/for-accounts.md)
>
> Access account billing information and FOCUS-compliant cost reports

> [!NOTE]
>
> For executing models and generating content, see [Model APIs](../documentation/model-apis/overview.md). To call models over HTTP, see the [Inference API](inference-api.md).
