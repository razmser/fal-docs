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

# fal environments

> Create, list, and delete fal environments such as staging with fal environments. Then target one with --env when you deploy apps, set secrets, or run code.

```bash theme={null}
Usage: fal environments [-h] [--debug] [--pdb] [--cprofile] command ...

Manage fal environments.

Options:
  -h, --help  show this help message and exit

Commands:
  command
    list      List environments.
    create    Create an environment.
    delete    Delete an environment.
```

> [!NOTE]
>
> The `environments` command also has an alias `envs` for convenience:
>
> ```bash theme={null}
> fal envs list
> ```

## List

```bash theme={null}
Usage: fal environments list [-h] [--debug] [--pdb] [--cprofile]
                             [--output {pretty,json}] [--json]

List environments.

Options:
  -h, --help            show this help message and exit

Output:
  --output {pretty,json}
                        Modify the command output
  --json                Output in JSON format (same as --output json)

Examples:
  fal environments list
  fal envs list --output json
```

## Create

```bash theme={null}
Usage: fal environments create [-h] [--debug] [--pdb] [--cprofile]
                               [--description DESCRIPTION]
                               name

Create an environment.

Positional Arguments:
  name                  Environment name.

Options:
  -h, --help            show this help message and exit
  --description DESCRIPTION
                        Environment description.

Examples:
  fal environments create staging
  fal envs create dev --description "Development environment"
```

## Delete

```bash theme={null}
Usage: fal environments delete [-h] [--debug] [--pdb] [--cprofile] [--yes]
                               name

Delete an environment.

Positional Arguments:
  name        Environment name.

Options:
  -h, --help  show this help message and exit
  --yes       Skip confirmation prompt.

Debug:
  --debug     Show verbose errors.
  --pdb       Start pdb on error.
  --cprofile  Show cProfile report.
```

> [!WARNING]
>
> Deleting an environment permanently removes all apps and secrets in that environment. You will be prompted to confirm by typing the environment name unless `--yes` is provided.

## Using Environments with Other Commands

Once you've created environments, you can target them using the `--env` flag in other commands:

```bash theme={null}
# Deploy to an environment
fal deploy path/to/myapp.py::MyApp --env staging

# Manage secrets per environment
fal secrets set API_KEY=value --env staging
fal secrets list --env staging

# Manage apps per environment
fal apps list --env staging
fal apps scale my-app --min-concurrency 1 --env staging
fal apps runners my-app --env staging
fal apps delete my-app --env staging

# Run functions with environment-specific secrets
fal run path/to/myapp.py::MyApp --env staging
```
