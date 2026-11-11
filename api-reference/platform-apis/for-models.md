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

# Platform APIs for Models

> Programmatic access to model metadata, pricing, usage tracking, and analytics

The **fal Platform APIs** provide programmatic access to platform management features for Model APIs, including:

* **Model metadata** - Search and discover available model endpoints with detailed information
* **Pricing information** - Retrieve real-time pricing and estimate costs
* **Usage tracking** - Access detailed usage line items with unit quantities and prices
* **Analytics** - Query time-bucketed metrics for request counts, success/error rates, error-type breakdown, latency percentiles, cold boot metrics, and billable duration

## Available Operations

The Platform APIs provide the following endpoints for managing Model APIs:

> [card] [Model Search](../../platform-apis/v1/models.md)
>
> Search and discover available model endpoints with metadata, categories, and capabilities

> [card] [Model Pricing](../../platform-apis/v1/models/pricing.md)
>
> Retrieve real-time pricing information for models

> [card] [Estimate Cost](../../platform-apis/v1/models/pricing/estimate.md)
>
> Estimate costs for planned operations

> [card] [Usage](../../platform-apis/v1/models/usage.md)
>
> Access detailed usage line items with unit quantities and prices

> [card] [Analytics](../../platform-apis/v1/models/analytics.md)
>
> Query time-bucketed metrics for requests, success rates, error-type breakdown, latency percentiles, cold boot metrics, and billable duration

> [card] [List Requests by Endpoint](../../platform-apis/v1/models/requests/by-endpoint.md)
>
> List recent requests for a specific model endpoint with filters and pagination

> [card] [Delete Request Payloads](../../platform-apis/v1/models/requests/payloads.md)
>
> Delete IO payloads and CDN output files for a specific request

> [!NOTE]
>
> These APIs are for **platform management** of Model APIs. For executing models and generating content, see the [Inference Methods](../../documentation/model-apis/inference.md) documentation.
