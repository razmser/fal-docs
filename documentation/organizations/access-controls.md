> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Model Access Controls

> Restrict which models your team members can access via the API and Playground.

Model Access Controls let organization admins restrict which models team members can call. Use them to enforce compliance (only approved models), control costs (block expensive models), or limit usage to models that meet your security requirements. Access controls are configured from the [Access Controls dashboard](https://fal.ai/dashboard/access-controls).

Controls are enforced separately for two contexts. **API Access** controls whether a model can be called via the fal client SDKs or REST API. **UI Access** controls whether a model can be run from the Playground and Sandbox. This separation lets you allow models in the API for production use while blocking them from the Playground, or vice versa.

## Access Statuses

Each model can be set to one of three statuses:

| Status | Behavior |
| - | - |
| **Allowed** | Model can be used in the given context |
| **Blocked** | Model is blocked. API calls return an error; Playground shows an access control message. |
| **Inherit** | Uses the parent organization's setting (child teams only) |

> [!NOTE]
>
> Blocked models still appear in the Model Gallery and API documentation. Access controls only affect execution.

## Organization Inheritance

When access controls are enabled, rules set at the org level flow down to all child teams by default. Child teams default to **Inherit** and follow the org's rules, but they can override specific models when they need different access.

```
Organization: FLUX.1 = Allowed
  ├── Team A: FLUX.1 = Inherit (uses org setting: Allowed)
  ├── Team B: FLUX.1 = Blocked (overrides org)
  └── Team C: FLUX.1 = Inherit (uses org setting: Allowed)
```

The dashboard shows which rules are inherited vs set at the team level.

## Enterprise Ready Models

fal designates certain models as "Enterprise Ready" based on licensing, compliance, and reliability criteria. By default, any model marked Enterprise Ready is automatically allowed and you only need to manage exceptions by blocking specific models you don't want.

For stricter control, you can switch to **Require Explicit Approval** mode where all models are blocked by default and must be individually approved, even Enterprise Ready ones.

> [!WARNING]
>
> Switching to explicit approval may disrupt existing usage. Models that were previously auto-allowed will be blocked until explicitly approved.

You can opt in to receive email notifications when new models are designated as Enterprise Ready, so you can review and approve them promptly.

## What Happens When a Model Is Blocked

| Context | Behavior |
| - | - |
| **API call** | Returns an error indicating the model is restricted for your account |
| **Playground** | Model appears but execution is blocked with an access control message |
| **Sandbox** | Model appears but cannot be included in comparison runs |

> [!NOTE]
>
> Model Access Controls are available on enterprise plans. Contact [sales](https://fal.ai/enterprise#contact-sales) to enable this feature for your organization.

> [card] [Managing Teams](managing-teams.md)
>
> Configure org-wide policies and member management
