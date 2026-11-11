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

# fal keys

> Create, list, and revoke fal API keys from the command line with fal keys. Choose the ADMIN or API scope for each key, and add an optional key description.

```bash theme={null}
Usage: fal keys [-h] [--debug] [--pdb] [--cprofile] command ...

Manage fal keys.

Options:
  -h, --help  show this help message and exit
Commands:
  command
    create    Create a key.
    list      List keys.
    revoke    Revoke key.
```

## Create

```bash theme={null}
Usage: fal keys create [-h] [--debug] [--pdb] [--cprofile] --scope {ADMIN,API}
                       [--desc DESC]

Create a key.

Options:
  -h, --help           show this help message and exit
  --scope {ADMIN,API}  The privilege scope of the key.
  --desc DESC          Key description (e.g. "My Test Key")
```

## List

```bash theme={null}
Usage: fal keys list [-h] [--debug] [--pdb] [--cprofile]

List keys.

Options:
  -h, --help  show this help message and exit
```

## Revoke

```bash theme={null}
Usage: fal keys revoke [-h] [--debug] [--pdb] [--cprofile] key_id

Revoke key.

Positional Arguments:
  key_id      Key ID.

Options:
  -h, --help  show this help message and exit
```
