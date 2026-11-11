> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../quickstart.md) and the [Model APIs overview](../../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Installation & Setup

> Complete setup guide for the fal CLI and development environment. This guide covers all platforms and authentication methods.

The fal CLI is the primary tool for developing, testing, and deploying Serverless applications. This page covers installing the CLI, authenticating with your account, and switching between team contexts. By the end, you will have a working development environment ready for the [Quick Start](quick-start.md).

If you are only using [Model APIs](../../model-apis/overview.md) to call pre-trained models, you do not need the CLI. You only need an [API key](https://fal.ai/docs/documentation/model-apis/authentication/index.md) and the client SDK (`fal-client` for Python or `@fal-ai/client` for JavaScript). The CLI is specifically for the Serverless workflow: writing, testing, and deploying your own apps. For an overview of how accounts, keys, and profiles relate to each other, see [Accounts and Identity](../../setting-up/accounts-and-identity.md).

> [!NOTE]
>
> **Serverless access is approved per account.** A fal account is free. To deploy your own apps on fal Serverless, the fal team must approve access for your account. Request access on the [Serverless Get Started page](https://fal.ai/dashboard/serverless-get-started). You do not need this approval to call [Model APIs](../../model-apis/overview.md).

## System Requirements

* **Python**: 3.8 or later
* **Operating System**: macOS, Linux, or Windows

## Install the CLI

### Using pip

```bash theme={null}
pip install fal
```

### Verify Installation

Check that the CLI installed correctly:

```bash theme={null}
fal --version
```

You should see the version number printed.

## Authentication

### Option 1: Interactive Login (Recommended)

```bash theme={null}
fal auth login
```

This opens your browser to authenticate with fal. Once complete, your credentials are saved locally.

> [!TIP]
>
> If you are a member of multiple teams, the login process will prompt you to select which team account you want to use as your default. This affects which resources you can access and deploy to.

### Option 2: API Key

Create an API key at [API Keys](https://fal.ai/dashboard/keys) on the `SERVERLESS` preset. For full CLI access, use the `FULL` preset. See [Key permissions](../../model-apis/authentication/key-based.md#permissions). If creating a key for a team, make sure to select your team on the top left corner.

Set it as an environment variable:

```bash theme={null}
export FAL_KEY="your-api-key-here"
```

Or add it to your shell profile (`~/.bashrc`, `~/.zshrc`, etc.) to persist across sessions.

If you work with multiple API keys (for example, personal and team accounts), you can use `fal profile` to save keys and switch between them. See the [Accounts and Identity](../../setting-up/accounts-and-identity.md#switching-accounts) page for details on profiles versus team switching.

## Team Accounts

After authentication, you can switch between different teams or your personal account using the `fal team` command.

### View Available Teams

To list teams that you are a member of:

```bash theme={null}
fal team list
```

| Default | Team | Full Name | ID |
| - | - | - | - |
| \* | myteam | My Team | github\|1kbr8zjkk377xfs5tl2erl4x |
| | otherteam | Other Team | github\|v11vo11w2kqakm99ke00258q |

The `*` indicates your currently active team.

### Switch Teams

To switch to a different team:

```bash theme={null}
fal team set otherteam
```

To switch back to your personal account:

```bash theme={null}
fal team unset
```

## Verify Authentication

Test your authentication setup:

```bash theme={null}
fal auth whoami
```

This should display your account information, including your current team (if any). Example output:

```
User: john@example.com
Team: myteam (My Team)
```

If you see an error, double-check your authentication method and API key configuration.

## Upgrading

Keep your CLI up to date:

```bash theme={null}
pip install --upgrade fal
```

Check for breaking changes in the [changelog](https://github.com/fal-ai/fal/releases).

## Getting Help

If you run into issues, `fal --help` and `fal COMMAND --help` provide inline documentation for every command. For community support, join the [fal Discord](https://discord.gg/fal-ai). To report bugs or request features, open an issue on [GitHub](https://github.com/fal-ai/fal/issues).
