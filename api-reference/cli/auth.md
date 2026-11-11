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

# fal auth

> Log in to fal from the CLI with GitHub, Google, or an enterprise SSO domain. Log out with fal auth logout, and show the current user with fal auth whoami.

```bash theme={null}
fal auth [-h] [--debug] [--pdb] [--cprofile] command ...

Authenticate with fal.

Options:
  -h, --help  show this help message and exit

Commands:
  command
    login     Log in a user.
    logout    Log out the currently logged-in user.
    whoami    Show the currently authenticated user.
```

## Login

```bash theme={null}
Usage: fal auth login [-h] [--debug] [--pdb] [--cprofile]
                      [--connection CONNECTION] [--no-browser]

Log in a user.

Options:
  -h, --help               show this help message and exit
  --connection CONNECTION  Auth connection (e.g. github, google, or an SSO domain). Skips the interactive prompt.
  --no-browser             Don't attempt to open a browser. Just print the URL to visit.
```

When `--connection` is omitted, the CLI prompts you to choose between GitHub, Google, or your enterprise SSO domain. The previously used choice is remembered and offered as the default on subsequent logins.

## Logout

```bash theme={null}
Usage: fal auth logout [-h] [--debug] [--pdb] [--cprofile] [--no-browser]

Log out the currently logged-in user.

Options:
  -h, --help    show this help message and exit
  --no-browser  Don't attempt to open a browser. Just print the URL to visit.
```

## Whoami

```bash theme={null}
Usage: fal auth whoami [-h] [--debug] [--pdb] [--cprofile]

Show the currently authenticated user.

Options:
  -h, --help  show this help message and exit

Debug:
  --debug     Show verbose errors.
  --pdb       Start pdb on error.
  --cprofile  Show cProfile report.
```
