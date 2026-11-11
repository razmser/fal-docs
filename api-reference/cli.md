> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../documentation/quickstart.md) and the [Model APIs overview](../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# CLI Reference

> Complete reference for the fal command-line interface

The fal CLI is the primary tool for deploying and managing your fal applications.

## Installation

```bash theme={null}
pip install fal
```

## Authentication

```bash theme={null}
fal auth login
```

## Commands

| Command | Description |
| - | - |
| [`fal auth`](cli/auth.md) | Authenticate with fal |
| [`fal deploy`](cli/deploy.md) | Deploy an application |
| [`fal run`](cli/run.md) | Run a function |
| [`fal apps`](cli/apps/list.md) | Manage applications |
| [`fal environments`](cli/environments.md) | Manage environments |
| [`fal keys`](cli/keys.md) | Manage API keys |
| [`fal secrets`](cli/secrets.md) | Manage secrets |
| [`fal files`](cli/files.md) | Manage files in /data |
| [`fal queue`](cli/queue.md) | Manage queued requests |
| [`fal runners`](cli/runners.md) | Manage runners |
| [`fal api`](cli/api.md) | Call a fal API endpoint directly |
| [`fal account`](cli/teams.md) | Manage accounts |
| [`fal doctor`](cli/doctor.md) | Diagnose issues |
| [`fal create`](cli/create.md) | Create a new project |
| [`fal profile`](cli/profile.md) | Manage profiles |
