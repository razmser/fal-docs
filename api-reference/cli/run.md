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

# fal run

> Run a fal function or app on a temporary runner with fal run. Choose the auth mode, environment, and machine type, or use --local to run it on your machine.

```bash theme={null}
Usage: fal run [-h] [--team TEAM] [--no-cache] [--app-name APP_NAME]
               [--auth AUTH] [--env ENV] [--local]
               [--exposed-port PORT] [--exposed-metrics-port PORT]
               [--machine-type MACHINE_TYPE]
               [--limit-max-requests LIMIT_MAX_REQUESTS]
               func_ref

Run fal function.

Positional Arguments:
  func_ref              Function reference. Configure team in pyproject.toml for app names.

Options:
  -h, --help            show this help message and exit
  --team TEAM           The team to use.
  --no-cache            Do not use the cache for the environment build.
  --app-name APP_NAME   Application name to run with.
  --auth AUTH           Application authentication mode (private, public, shared), defaults to public.
  --env ENV             Target environment (defaults to main).
  --local               Run locally without serverless.
  --exposed-port PORT   Port for the --local app server (default: 8080).
  --exposed-metrics-port PORT
                        Port for the --local metrics server (default: 9090).
  --machine-type MACHINE_TYPE
                        Machine type to use for this run.
  --limit-max-requests LIMIT_MAX_REQUESTS
                        For fal.App runs, gracefully stop the server after serving N requests.

Examples:
  fal run path/to/myfile.py::myfunc
  fal run path/to/myfile.py::myfunc --env staging
  fal run path/to/myfile.py::MyApp --auth private
  fal run path/to/myfile.py::MyApp --local
  fal run path/to/myfile.py::MyApp --machine-type GPU-A100
```

> [!WARNING]
>
> `fal run` ignores the `auth` set on `fal.App` and in `pyproject.toml` and defaults to `public` so the app is reachable for testing. Pass `--auth` to override. In a future major release the default will become `private` and the `fal.App`/`pyproject.toml` value will be respected.

## Authentication Modes

The `--auth` flag controls who can access your app while it's running:

* **`public`** (default for `fal run`): Anyone can call your app without authentication. You pay for all usage.
* **`private`**: Only you (or your team) can call the app. Requires a valid API key.
* **`shared`**: Any authenticated fal user can call the app.

By default, `fal run` uses `public` mode for easy testing during development.
