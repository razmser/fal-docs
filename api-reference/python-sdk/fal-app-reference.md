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

# Python SDK

> API reference for the fal Python package

The `fal` package provides decorators and utilities for building serverless AI applications.

## Installation

```bash theme={null}
pip install fal
```

## Quick Start

```python theme={null}
import fal

class MyApp(fal.App):
    @fal.endpoint("/")
    def run(self) -> dict:
        return {"message": "Hello, World!"}
```

Deploy with:

```bash theme={null}
fal deploy my_app.py
```

See the [Quick Start guide](https://fal.ai/docs/serverless/getting-started/quick-start.md) for a complete walkthrough.

## API Reference

> [card] [fal](https://fal.ai/docs/reference/serverless-sdk/fal.md)
>
> `App` class, decorators (`endpoint`, `realtime`, `function`, `cached`), `HealthCheck`, `ContainerImage`

> [card] [fal.api](https://fal.ai/docs/reference/serverless-sdk/fal.api.md)
>
> `SyncServerlessClient`: manage apps, runners, keys, secrets, and deploy programmatically

> [card] [fal.toolkit](https://fal.ai/docs/reference/serverless-sdk/fal.toolkit.md)
>
> File types (`Image`, `Video`, `Audio`), `KVStore`, model downloads, GPU utilities

> [card] [fal.exceptions](https://fal.ai/docs/reference/serverless-sdk/fal.exceptions.md)
>
> Exception classes for error handling

> [card] [CLI](https://fal.ai/docs/reference/cli/installation.md)
>
> `fal deploy`, `fal apps`, `fal runners`, `fal keys`, `fal secrets`, and more

## Key Decorators

| Decorator | Description |
| :- | :- |
| `@fal.endpoint(path)` | Define an HTTP endpoint on an App |
| `@fal.realtime(path)` | WebSocket endpoint for realtime applications |
| `@fal.function(...)` | Create a standalone serverless function |
| `@fal.cached` | Cache function results in-memory |

See the [fal module reference](https://fal.ai/docs/reference/serverless-sdk/fal.md) for full details.

## App Configuration

Configure your app using class variables. See the [full App reference](https://fal.ai/docs/reference/serverless-sdk/fal.md#app) for all options.

| Property | Description |
| :- | :- |
| `machine_type` | GPU type: `"GPU-A100"`, `"GPU-H100"`, or list for fallback (see [Machine Types](../../documentation/deployment/machine-types.md)) |
| `requirements` | Pip packages to install (e.g., `["torch>=2.0"]`) |
| `requirements_context_dir` | Base directory for resolving local-path requirements such as `.[extra]` or `projects/foo[extra]` |
| `num_gpus` | Number of GPUs to allocate |
| `min_concurrency` | Minimum warm instances (1+ to avoid cold starts) |
| `max_concurrency` | Maximum instances to scale to |
| `request_timeout` | Maximum seconds per request |
| `startup_timeout` | Maximum seconds for setup |
| `app_auth` | Auth mode: `"private"`, `"public"`, or `"shared"` |
| `app_files` | Files/directories to include in deployment |
| `image` | Custom `ContainerImage` for the application |

## Toolkit Highlights

The [fal.toolkit](https://fal.ai/docs/reference/serverless-sdk/fal.toolkit.md) module provides utilities for common tasks:

| Utility | Description |
| :- | :- |
| `Image`, `Video`, `Audio` | File types with automatic CDN upload |
| `KVStore` | Persistent key-value storage |
| `download_model_weights()` | Download and cache model weights |
| `clone_repository()` | Clone git repositories |
| `sync_dir()` | Sync local directories to remote storage |
| `get_gpu_type()` | Detect current GPU type |

## SyncServerlessClient

The [SyncServerlessClient](https://fal.ai/docs/reference/serverless-sdk/fal.api.md#syncserverlessclient) provides programmatic access to fal operations (mirrors the CLI):

```python theme={null}
from fal.api import SyncServerlessClient

client = SyncServerlessClient()

# Manage apps
apps = client.apps.list()
client.apps.scale("my-app", max_concurrency=10)

# Manage runners
runners = client.runners.list()
client.runners.stop("runner-id")

# Manage keys and secrets
client.keys.create(scope="admin")
client.secrets.set("API_KEY", "value")

# Deploy
client.deploy("path/to/app.py::MyApp")
```

For detailed guides on building applications, see the [Serverless documentation](https://fal.ai/docs/serverless.md).
