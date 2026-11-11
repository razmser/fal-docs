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

# Platform APIs for Organizations

> Programmatic access to organization-wide team listings, cross-team usage records, FOCUS billing reports, and billing events

The **fal Platform APIs** provide programmatic access to organization-level data spanning every team and product line, including:

* **Team listings** - List all teams in your organization and identify the root team
* **Usage records** - Browse cross-team, cross-product usage attributed to each team and product line
* **FOCUS reports** - Download a FinOps FOCUS-compliant billing report as CSV, spanning every team in your organization
* **Billing events** - Browse paginated per-request billing event records across all teams, with cost breakdowns in USD

## Available Operations

The Platform APIs provide the following endpoints for organization administration:

> [card] [Organization Teams](../../platform-apis/v1/organization/teams.md)
>
> List the teams in your organization with their details, including the root team

> [card] [Organization Usage](../../platform-apis/v1/organization/usage.md)
>
> Browse paginated usage records across all teams and product lines, with per-team and per-product attribution

> [card] [Organization FOCUS Report](../../platform-apis/v1/organization/focus.md)
>
> Download a FOCUS-compliant billing report as CSV spanning every team, with per-team sub-account attribution under pooled billing

> [card] [Organization Billing Events](../../platform-apis/v1/organization/billing-events.md)
>
> Browse paginated per-request billing events across all teams, with per-team attribution and USD cost breakdowns

> [!NOTE]
>
> These endpoints are available to enterprise customers with organizations enabled. Call them with a key on the `BILLING` preset that belongs to the root team of the organization. The Organization FOCUS Report also requires FOCUS reports to be enabled. Contact your account team or [support@fal.ai](mailto:support@fal.ai) to request access.
