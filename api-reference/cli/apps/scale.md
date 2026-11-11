> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../../documentation/quickstart.md) and the [Model APIs overview](../../../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# fal apps scale

> Change the scaling settings of a deployed fal app with fal apps scale. Set keep-alive, concurrency, multiplexing, timeouts, machine types, and regions.

```bash theme={null}
Usage: fal apps scale [-h] [--debug] [--pdb] [--cprofile] [--team TEAM]
                      [--keep-alive KEEP_ALIVE]
                      [--max-multiplexing MAX_MULTIPLEXING]
                      [--max-concurrency MAX_CONCURRENCY]
                      [--min-concurrency MIN_CONCURRENCY]
                      [--concurrency-buffer CONCURRENCY_BUFFER]
                      [--concurrency-buffer-perc CONCURRENCY_BUFFER_PERC]
                      [--scaling-delay SCALING_DELAY]
                      [--request-timeout REQUEST_TIMEOUT]
                      [--startup-timeout STARTUP_TIMEOUT]
                      [--machine-types MACHINE_TYPES [MACHINE_TYPES ...]]
                      [--regions REGIONS [REGIONS ...]]
                      [--env ENV]
                      app_name

Scale application.

Positional Arguments:
  app_name              Application name.

Options:
  -h, --help            show this help message and exit
  --team TEAM           The team to use.
  --keep-alive KEEP_ALIVE
                        Keep alive (seconds).
  --max-multiplexing MAX_MULTIPLEXING
                        Maximum multiplexing
  --max-concurrency MAX_CONCURRENCY
                        Maximum concurrency.
  --min-concurrency MIN_CONCURRENCY
                        Minimum concurrency
  --concurrency-buffer CONCURRENCY_BUFFER
                        Concurrency buffer (minimum extra capacity).
  --concurrency-buffer-perc CONCURRENCY_BUFFER_PERC
                        Concurrency buffer expressed as a percentage.
  --scaling-delay SCALING_DELAY
                        Scaling delay (seconds).
  --request-timeout REQUEST_TIMEOUT
                        Request timeout (seconds). If a request takes longer, it is aborted and the runner gracefully stopped as it could be in a bad state.
  --startup-timeout STARTUP_TIMEOUT
                        Startup timeout (seconds).
  --machine-types MACHINE_TYPES [MACHINE_TYPES ...]
                        Machine types (pass several items to set multiple).
  --regions REGIONS [REGIONS ...]
                        Valid regions (pass several items to set multiple).
  --env ENV             Target environment (defaults to main).
```

> [!NOTE]
>
> **Note:**
>
> Redeploying the application, by default, will not reset these settings, except for the code-specific settings. See [Code-Specific Settings (Reset on Deploy)](https://fal.ai/docs/serverless/deployment-operations/scale-your-application.md#code-specific-settings-reset-on-deploy) for details.
