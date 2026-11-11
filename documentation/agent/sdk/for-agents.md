> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../quickstart.md) and the [Model APIs overview](../../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Use the docs with coding agents

> Give a coding agent the exact guides and references needed to build an Agent SDK integration.

Start with the [quickstart](quickstart.md), [method reference](methods.md), and [type reference](types.md).
Give your coding agent the installed package version and the guide for the task you want to implement.

## Find the right page

| Task | Read |
| - | - |
| Configure the client | [Configuration](configuration.md). |
| Create, stream, recover, or cancel a task | [Responses and recovery](responses.md). |
| Answer questions or approvals | [Questions and approvals](inputs.md). |
| Edit or execute a plan | [Plans](plans.md). |
| Read artifacts or costs | [Media and costs](media.md). |
| Read conversation history | [Conversations](resources.md). |
| Manage projects, documents, or memory | [Projects](projects.md). |
| Choose models or defaults | [Models and settings](settings.md). |
| Manage queued work or generation attempts | [Queues and runs](queue.md). |
| Manage assets, collections, or smart entities | [Library](library.md). |
| Manage skills or read tool activity | [Skills](skills.md) and [activity](activity.md). |
| Diagnose a failure | [Errors](errors.md). |

Use the page's **Copy page** action to copy Markdown.
Mintlify also serves Markdown page exports and a documentation index at `llms.txt`.
Use the index from the same documentation host as the page you are reading.
