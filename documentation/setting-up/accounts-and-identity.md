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

# Accounts and Identity

> Set up your fal account, choose between personal and team workspaces, and understand how identity works on the platform.

The first step to using fal is setting up your account. When you sign up, you get a personal account that comes with its own [API keys](https://fal.ai/docs/documentation/model-apis/authentication.md), deployed apps, and billing. If you're working with others, you can create a [team](teams.md) so everyone shares a single set of API keys, deployments, and billing instead of managing their own. This is useful whether you are consuming [Model APIs](../model-apis/overview.md) together or deploying your own models with [Serverless](../serverless.md).

Your identity on fal is separate from the accounts you operate under. You log in as yourself, then choose which account context to work in, whether that's your personal account or a team you belong to. This means one person can switch between multiple accounts without logging out.

## Account Types

| Account type | What it is |
| - | - |
| **Personal** | Created when you sign up. Your individual account. |
| **Team** | A shared workspace with its own API keys, apps, and billing. Members are invited and assigned roles. |
| **Organization** | An enterprise parent that manages multiple teams with centralized policies and SSO. |

Teams can exist on their own (standalone) or as children of an [organization](../organizations.md). Standalone teams work well for small groups. Organizations add centralized policies, SSO, and cross-team visibility. Each team maintains its own API keys, secrets, deployed apps, and billing, completely separate from your personal account.

## Sign In

fal supports GitHub OAuth, Google OAuth, and SSO/SAML. If your company has SSO configured, you'll use that automatically. Otherwise, sign in with GitHub or Google. All methods are browser-based and take you straight to the [dashboard](https://fal.ai/dashboard).

> [!WARNING]
>
> **Each login method creates a separate account.** If you sign in with Google and later sign in with SSO using the same email, you will have two separate personal accounts with separate API keys, credits, and deployed apps. Social logins (Google + GitHub) with the same email are merged into one account, but SSO and social logins are always kept separate. If you have SSO enabled for your organization, use SSO consistently to avoid duplicate accounts.

## Get Your API Key

All requests to fal require authentication. Create an API key in the [dashboard](https://fal.ai/dashboard/keys), then set it as an environment variable:

```bash theme={null}
export FAL_KEY="your-api-key-here"
```

When you create a key, you select a preset that controls what the key can reach. The `API` preset covers calls to any model on fal, including [Model APIs](../model-apis/overview.md) and your own deployed endpoints. The `SERVERLESS` preset adds the CLI operations that [Serverless](../serverless.md) deployments need, such as `fal deploy` and `fal run`. See [Key permissions](../model-apis/authentication/key-based.md#permissions) for every preset.

> [!WARNING]
>
> API keys belong to **accounts**, not people. A team's API key is shared by all team members and accesses that team's resources. When creating a key, make sure the correct account is selected in the top-left corner of the dashboard.

## Switching Between Accounts

All operations, including API calls, deployments, and billing, happen under whichever account is active. You can switch accounts in two ways depending on how you interact with fal.

In the [dashboard](https://fal.ai/dashboard), select the account in the top-left corner. This controls which account's resources you see and which account new API keys are scoped to.

If you're using the fal CLI, you have two options. The recommended approach is [`fal auth login`](../../api-reference/cli/auth.md), which opens a browser-based login flow and lets you select your team account interactively. Alternatively, use [`fal teams set`](../../api-reference/cli/teams.md) to switch the active account for all subsequent commands, or pass `--team` on any command.

```bash theme={null}
# Browser-based login (recommended for SSO users)
fal auth login

# Switch your CLI context to a team
fal teams set my-company

# Or target a team for a single command
fal deploy my_app.py::MyApp --team my-company
```

You can also use [`fal profile`](../../api-reference/cli/profile.md) to manage multiple named profiles, each with its own API key and host. This is useful if you work across multiple accounts or environments and want to switch between them without re-authenticating.

```bash theme={null}
fal profile create staging
fal profile key  # set API key for this profile
fal profile set staging  # switch to this profile
fal profile unset  # go back to default
```

## Next Steps

> [card] [Teams](teams.md)
>
> Create shared workspaces with roles, API keys, and request attribution

> [card] [Organizations](../organizations.md)
>
> Enterprise policies, SSO, and multi-team management

> [card] [Data Retention & Storage](../model-apis/media-expiration.md)
>
> How generated media is stored and how to control file expiration

> [card] [Resources](resources.md)
>
> Client libraries, API references, and community links
