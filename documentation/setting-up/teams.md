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

# Teams

> Create shared workspaces with their own API keys, deployments, and billing. Manage members, roles, and understand how request attribution works.

Teams let multiple people share a single set of API keys, deployed apps, and billing on fal. Instead of each person managing their own [personal account](accounts-and-identity.md), a team acts as a shared workspace where everyone operates under the same identity. This is useful whether you are calling [Model APIs](../model-apis/overview.md) together or deploying your own models with [Serverless](../serverless.md).

Teams can exist on their own (standalone) or as children of an [organization](../organizations.md). Standalone teams work well for small groups. Organizations add centralized policies, SSO, and cross-team visibility. Each team maintains its own API keys, secrets, deployed apps, and billing, completely separate from any member's personal account. For switching between your personal account and a team, see [Switching Between Accounts](accounts-and-identity.md#switching-between-accounts).

## Creating a Team

**1. Open the team menu**

Click your account name in the top-left of the
[Dashboard](https://fal.ai/dashboard) and select **Create Team**.

**2. Name your team**

Enter a team name. This will be visible to all members.

**3. Start using it**

You'll be switched to the new team automatically. Create API keys and invite
members from here.

## Inviting Members

**1. Go to Members**

Navigate to [**Dashboard > Members**](https://fal.ai/dashboard/members)

**2. Click Invite**

Enter the member's email address and select their role.

**3. They accept**

The invited user receives an email with a link to join your team.

For larger teams, use the bulk invite feature at [**Dashboard > Members > Bulk**](https://fal.ai/dashboard/members/bulk). Upload a CSV with columns:

```
email,role
alice@company.com,admin
bob@company.com,developer
carol@company.com,billing
```

The `role` column accepts `admin`, `billing`, `developer`, `creator`, and `viewer`, in any letter case. It also accepts two older names. `member` is the same as `developer`, and `manager` is the same as `billing`.

Up to 50 invites can be sent per batch.

## Roles

Each team member is assigned one of five roles that control what they can do in the dashboard. Roles govern dashboard and management access. All team members, regardless of role, can use the team's API keys to call models, deploy apps, and run workloads.

| Role | What they can do |
| - | - |
| **Admin** | Full team management: create and delete API keys, view billing and invoices, invite members, and change roles. |
| **Developer** | Standard access to team resources. Cannot manage API keys, billing, members, or roles. |
| **Billing** | Everything a Developer can do, plus billing: invoices, payment methods, credit purchases, and usage. Can view API keys but cannot create or delete them. Cannot invite members or change roles. |
| **Creator** | Use Playground, Sandbox, and Workflows. Cannot view request logs or manage API keys, billing, members, or roles. |
| **Viewer** | Read-only access to team resources. Can view request logs and the library. Cannot run models, change anything, or manage API keys, billing, members, or roles. |

> [!TIP]
>
> Start with **Admin** for team leads who need to manage keys and members. Use
> **Developer** for engineers who only need to deploy and monitor apps. Use
> **Billing** for members who need Developer access plus usage, invoices, and
> payments, but not API key or member management. Use
> **Creator** for members who only need the Playground, Sandbox, and Workflows.
> Use **Viewer** for members who only need read-only access.

## API Keys and Request Attribution

Roles and key presets draw on one permission model. A role grants permissions to a person in the dashboard. A preset grants permissions to a key. The two callers are different, so their permissions can differ. A Developer-role member cannot create keys in the dashboard. The same member can use a team key on the `SERVERLESS` preset to run `fal deploy`.

API keys belong to the team, not to individual members. Any team member can use a team API key, and only Admins can create or delete one. When you create a key, select the correct team in the top-left of the dashboard. See [Key permissions](../model-apis/authentication/key-based.md#permissions) for the available presets.

When you use `fal auth login` and select a team, each request carries your individual identity within the team.

Request-log access depends on app ownership, not request attribution. Non-owner callers receive no request logs. For apps your team owns, see [Logging](../development/logging.md#request-logs).

When you use a team API key directly, requests are attributed to the team rather than to a specific member. Both methods access the same team resources and billing.

The fal client SDKs (`fal_client` in Python, `@fal-ai/client` in JavaScript) support both methods automatically. The SDK checks for credentials in this order:

1. `FAL_KEY` environment variable (sends `Authorization: Key ...`)
2. Tokens saved by `fal auth login` (sends `Authorization: Bearer ...`)

Login tokens are stored on disk at `~/.fal/auth0_token`, so they persist across terminal sessions and reboots. You run `fal auth login` once, and any Python script on the same machine picks up the credentials automatically, even from a completely different process or terminal. This works the same way as `gcloud auth login` or `aws sso login`.

```bash theme={null}
# Run once from any terminal -- persists to disk
fal auth login
fal teams set my-team
```

```python theme={null}
# Any Python script, any terminal session, any process
# Automatically uses your login-based identity (no FAL_KEY needed)
import fal_client

result = fal_client.subscribe("fal-ai/flux/schnell", arguments={
    "prompt": "a sunset"
})
```

If `FAL_KEY` is set, it takes priority over login tokens. To force login-based auth even when `FAL_KEY` is present, set `FAL_FORCE_AUTH_BY_USER=1`.

For production apps and CI pipelines, API keys are recommended because they don't expire and don't require interactive login. For development, testing, and any workflow where per-user attribution matters, `fal auth login` is preferred.

## Managing Your Team

Team management happens on the [Members page](https://fal.ai/dashboard/members) and is restricted to Admins. From there you can change a member's role using the dropdown next to their name, remove members to revoke their access, or cancel pending invites that haven't been accepted yet. If a team is no longer needed, Admins can archive it permanently from the same page.

## Next Steps

> [card] [Organizations](../organizations.md)
>
> Enterprise policies, SSO, and multi-team management

> [card] [Access Controls](../organizations/access-controls.md)
>
> Control which models your teams can access
