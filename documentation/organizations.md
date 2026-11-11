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

# Organizations

> Centralized management for teams, billing, and model access across your company.

Organizations give enterprise customers a parent account that manages multiple [teams](setting-up/teams.md) under a single umbrella. While standalone teams work well for small groups, organizations add centralized policies, SSO enforcement, cross-team billing visibility, and model access controls. Every team within an organization maintains its own API keys, secrets, deployed apps, and billing, but inherits policies set at the organization level.

If you are already using teams on fal and need centralized control over team creation, member management, or which models your teams can access, an organization is the next step. Organizations are available on enterprise plans. Contact [sales](https://fal.ai/enterprise#contact-sales) to get started.

## Organizations vs Teams

A team is a single group with its own API keys, secrets, apps, and billing. An organization is a parent account that manages multiple teams with shared policies.

```
Organization (root)
├── Team A (e.g., Research)
├── Team B (e.g., Production)
└── Team C (e.g., Platform)
```

Each child team operates independently for day-to-day work. The organization controls policies that apply across all teams, like who can create new teams, whether members need SSO, and which models are allowed.

## What Org Admins Can Do

Organization admins have centralized control over the full team lifecycle. They can create and archive teams, view and manage members across all teams (roles, assignments, invites), and configure policies that apply organization-wide. On the billing side, org admins can view invoices across all teams and set spending alerts. For security, they can enforce SSO for all team members and restrict which models teams can access via the API or Playground.

## Request Visibility

Organizations can enable **restricted request view** from the organization admin dashboard. When enabled, team members can see metadata for all team requests, including the endpoint, status, timing, and cost, but access to request content is restricted.

Request payloads are visible only to the member whose identity authenticated the request. The dashboard does not show which member made a request.

These restrictions do not grant access to app logs. Request logs remain restricted to the app owner, not non-owner callers. See [Logging](development/logging.md#request-logs) for details.

> [!WARNING]
>
> **Playground and API key submissions behave differently under restricted view:**
>
> * **Playground submissions** are authenticated with your personal login token. You can see your own Playground requests.
> * **API key submissions** are authenticated with the team's API key, not your personal identity. You cannot see API-submitted request payloads in the dashboard, even if you submitted them, because the API key identity does not match your personal login.
>
> If your team needs per-user request isolation for API submissions, contact support to discuss options.

## SSO Account Visibility

Organizations with SSO can restrict whether users can access their personal fal account alongside their SSO-linked team account. This ensures users only interact through the organization's managed teams. See [Managing Teams](organizations/managing-teams.md) for configuration details.

## Next Steps

> [card] [Managing Teams](organizations/managing-teams.md)
>
> Team lifecycle, member management, and org-wide policies

> [card] [Model Access Controls](organizations/access-controls.md)
>
> Restrict which models your teams can use
