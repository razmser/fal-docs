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

# fal queue

> Manage application queues.

```bash theme={null}
Usage: fal queue [-h] command ...

Manage application queues.

Options:
  -h, --help  show this help message and exit

Commands:
  command
    size      Get queue size for an application.
    flush     Flush all pending requests in an application queue.
```

## Size

```bash theme={null}
Usage: fal queue size [-h] [--output {pretty,json}] [--json] [--team TEAM]
                      [--by-user] app_name

Get queue size for an application.

Positional Arguments:
  app_name              Application name (do not prefix with owner).

Options:
  -h, --help            show this help message and exit
  --team TEAM           The team to use.
  --by-user             Group queue size by user.

Output:
  --output {pretty,json}
                        Modify the command output
  --json                Output in JSON format (same as --output json)
```

## Flush

```bash theme={null}
Usage: fal queue flush [-h] [--team TEAM] [--caller-user-id CALLER_USER_ID] app_name

Flush all pending requests in an application queue.

Positional Arguments:
  app_name              Application name.

Options:
  -h, --help            show this help message and exit
  --team TEAM           The team to use.
  --caller-user-id CALLER_USER_ID
                        Only flush requests from this user ID. If not provided, all requests will be flushed.
```
