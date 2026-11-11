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

# Managing Teams

> Control team lifecycle, member management, and organization-wide policies.

Organization administrators manage teams and members from the [Organization dashboard](https://fal.ai/dashboard/organization). This page covers the day-to-day operations of running an organization: creating and archiving teams, managing members across teams, and configuring the policies that govern how teams operate.

For an overview of what organizations are and when you need one, see [Organizations](../organizations.md). For controlling which models teams can access, see [Model Access Controls](access-controls.md).

## Team Lifecycle

Org admins can create new teams from the Organization dashboard. New teams are automatically associated with your organization and inherit org-wide policies. You can control whether child team admins are also allowed to create teams, or restrict team creation to org admins only.

When a team is no longer needed, org admins can archive it. Archived teams are locked and can no longer deploy apps, make API calls, or create compute instances. Existing deployed apps stop serving requests. To reverse an archive, contact the fal team.

> [!WARNING]
>
> Archiving a team locks the account and stops all deployed apps from serving requests.

## Team Roles

Every team member has one of five roles. The role determines what they can access within the team.

| Role | What they can do |
| - | - |
| **Admin** | Full team management: invite and remove members, manage API keys, configure scaling, set up [log drains](../serverless/observability/log-drains.md), and access all team settings. |
| **Developer** | Deploy and manage apps, view analytics, access logs, and use the API. This is the default role for new members. |
| **Billing** | Everything a Developer can do, plus billing: invoices, payment methods, spending alerts, and usage analytics. Can view API keys but cannot create or delete them. Cannot manage members. |
| **Creator** | Use Playground, Sandbox, and Workflows. Cannot view request logs or manage API keys, billing, members, or roles. |
| **Viewer** | Read-only access to team resources. Can view request logs and the library. Cannot run models, change anything, or manage API keys, billing, members, or roles. |

Admin and Billing roles can both access billing-related features. Only Admins can invite members, manage API keys, or configure log drains.

## Member Management

The Organization dashboard provides a unified view of all members across all teams. You can see which teams each member belongs to and their roles, filter by team or status (active, pending, unassigned), change roles or reassign members between teams, and onboard multiple members via CSV upload with team assignments.

Org admins can invite members to any team in the organization. You can also control whether child team admins are allowed to send invites themselves, or require all invitations to go through org admins.

> [!TIP]
>
> When SSO is configured, invites can require members to authenticate through your identity provider before joining.

## Organization Policies

These policies are configured from the Organization dashboard and apply across all child teams.

| Policy | What it controls |
| - | - |
| **Team Creation** | Whether child team admins can create new teams, or only org admins can. |
| **Invite Permissions** | Whether child team admins can invite new members, or all invitations must go through org admins. |
| **Cross-Team Visibility** | Whether admins and billing managers can see analytics and usage across all teams, not just their own. |
| **Request Visibility** | Whether team members see all requests made by their team, or only their own. Useful for organizations handling sensitive data. |
| **SSO Account Visibility** | Whether users can access their personal fal account alongside their SSO-linked team account. Disabling this ensures users only interact through the organization's teams. |

## Billing Across Teams

Each team in an organization maintains its own billing and subscription. Org admins with billing access can view invoices across all teams from the Organization dashboard, set spending alerts for any team, and monitor usage analytics across the organization (when cross-team visibility is enabled).

> [card] [Model Access Controls](access-controls.md)
>
> Control which models your teams can access
