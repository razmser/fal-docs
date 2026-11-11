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

# Platform APIs for Workflows

> Programmatic access to workflow creation, metadata, listing, and details

The **fal Platform APIs** provide programmatic access to workflow management for the authenticated user, including:

* **List workflows** - Paginated list of your workflows with optional search and filtering
* **Get workflow details** - Retrieve a specific workflow by owner and name, including its full definition
* **Create workflow** - Create a new workflow owned by the authenticated user

## Available Operations

The Platform APIs provide the following endpoints for Workflows:

> [card] [List Workflows](../../platform-apis/v1/workflows.md)
>
> List workflows for the authenticated user with optional search and filtering
> by endpoint

> [card] [Get Workflow Details](../../platform-apis/v1/workflows/get.md)
>
> Get detailed information about a specific workflow, including its full
> definition

> [card] [Create Workflow](../../platform-apis/v1/workflows/create.md)
>
> Create a new workflow owned by the authenticated user

## Authentication

All Workflows endpoints require authentication. Include your API key in the `Authorization` header:

```
Authorization: Key YOUR_API_KEY
```

List workflows returns only workflows owned by the authenticated user. Get workflow details requires access to the workflow (e.g., it is yours or public). Creating a workflow adds it to the authenticated user's namespace.

> [!NOTE]
>
> These APIs are for **platform management** of Workflows (creating, listing,
> and reading). To run workflows, use the [Model APIs](https://fal.ai/docs/model-apis.md) or workflow
> execution endpoints.
