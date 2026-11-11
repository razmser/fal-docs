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

# Platform APIs for Accounts

> Programmatic access to account billing information, FOCUS-compliant cost reports, and model access controls

The **fal Platform APIs** provide programmatic access to account-level billing, cost reporting, and governance, including:

* **Billing information** - Retrieve account billing details and credit balances
* **FOCUS reports** - Download FinOps FOCUS-compliant billing reports for cost analysis and interoperability
* **Model access controls** - Export the resolved UI and API access state for each model in your organization

## Available Operations

The Platform APIs provide the following endpoints for account billing, reporting, and governance:

> [card] [Account Billing](../../platform-apis/v1/account/billing.md)
>
> Retrieve billing information and credit balances for your account

> [card] [FOCUS Report](../../platform-apis/v1/account/focus.md)
>
> Download FOCUS-compliant billing reports from invoice or usage estimate data

> [card] [Model Access Controls](../../platform-apis/v1/account/model-access-controls.md)
>
> Export the resolved UI and API access state for each model in your organization

> [!NOTE]
>
> FOCUS reports and model access controls are available to enterprise customers with the corresponding features enabled. Contact your account team or [support@fal.ai](mailto:support@fal.ai) to request access.
