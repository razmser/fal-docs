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

# fal account

> Use fal account to list the personal, team, and organization accounts you belong to. Switch the active account for CLI commands, or go back to your own.

Manage and switch between the personal, team, and organization accounts you belong to. Useful when you're a member of multiple accounts and need to deploy or manage apps under a specific one.

## fal account list

List all accounts you belong to:

```bash theme={null}
fal account list
```

Shows your personal account along with any team and organization accounts, indicating which is currently active.

Supports `--output {pretty,json}` and `--json` for machine-readable output.

## fal account set

Switch to a specific account:

```bash theme={null}
fal account set <account>
```

`<account>` may be either an account nickname or the index shown by `fal account list`. After switching, all CLI operations (`fal deploy`, `fal apps`, `fal secrets`, etc.) run under the selected account.

If no `<account>` is provided, you'll be prompted interactively.

**Arguments:**

| Argument | Description |
| - | - |
| `account` | The account nickname (or list index) to switch to |

**Example:**

```bash theme={null}
# Switch to your company team
fal account set my-company

# Deploy under that team
fal deploy my_app.py::MyApp
```

## fal account unset

Switch back to your personal account:

```bash theme={null}
fal account unset
```
