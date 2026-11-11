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

# SyncServerlessClient

> Synchronous Python client for fal Serverless. Manage apps, runners, and deployments programmatically.

The `SyncServerlessClient` is the synchronous Python client for interacting with fal Serverless.
Its namespaces and methods mirror the CLI so you can automate the same workflows from Python.

## Import and Initialize

```python theme={null}
from fal.api import SyncServerlessClient

client = SyncServerlessClient(
    host=None,     # Optional. Override API host
    api_key=None,  # Optional. If omitted, read from env/profile
    profile=None,  # Optional. Named profile to use
    team=None,     # Optional. Team context for runner ops
)
```

## Overview of Namespaces

* `client.apps.*` — corresponds to `fal apps ...`
* `client.runners.*` — corresponds to `fal runners ...`
* `client.secrets.*` — corresponds to `fal secrets ...`
* `client.keys.*` — corresponds to `fal keys ...`
* `client.environments.*` — corresponds to `fal environments ...`
* `client.deploy(...)` — corresponds to `fal deploy ...`

## Apps Namespace

Manage your applications: list them, view runners for a specific app, and adjust scaling.

### List Applications (fal apps list)

```python theme={null}
apps = client.apps.list()

# Optional app name filter
filtered = client.apps.list(filter="stable")

# List apps in a specific environment
staging_apps = client.apps.list(environment_name="staging")
```

### List App Runners (fal apps runners my-app)

```python theme={null}
app_runners = client.apps.runners("my-app")

# Optional filters
recent = client.apps.runners("my-app", since=datetime.now() - timedelta(hours=1)) # last hour
running_only = client.apps.runners("my-app", state=["running"])                   # by state

# List runners for an app in a specific environment
staging_runners = client.apps.runners("my-app", environment_name="staging")
```

### Scale Application (fal apps scale)

Maps to CLI flags in `fal apps scale`. Any omitted option keeps the current value.

```python theme={null}
client.apps.scale(
    "my-app",
    keep_alive=300,            # seconds
    max_multiplexing=1,
    max_concurrency=10,
    min_concurrency=1,
    request_timeout=600,       # seconds
    startup_timeout=900,       # seconds
    machine_types=["GPU-H100", "GPU-H200"],
    regions=["us-east-1", "us-west-2"],
    concurrency_buffer=1,
    concurrency_buffer_perc=10,
    environment_name="staging",  # target environment
)
```

### Rollout Application (fal apps rollout)

```python theme={null}
client.apps.rollout("my-app")

# Force rollout
client.apps.rollout("my-app", force=True)

# Rollout in a specific environment
client.apps.rollout("my-app", environment_name="staging")
```

## Runners Namespace

List and manage runners.

### List Runners (fal runners list)

```python theme={null}
all_runners = client.runners.list()

# Recent runners only
last_10_min = client.runners.list(since=datetime.now() - timedelta(minutes=10))
```

## Keys Namespace

Create and manage API keys that the service uses to authenticate requests. The Python surface mirrors `fal keys ...`.

### Create Key (fal keys create)

```python theme={null}
from fal.sdk import KeyScope

# ADMIN keys can manage resources; API keys are limited to app invocation.
key_id, key_secret = client.keys.create(
    scope=KeyScope.ADMIN,          # or KeyScope.API
    description="CI deploy key",
)
print(f"Store securely: {key_id}:{key_secret}")  # secret shown only once
```

### List Keys (fal keys list)

```python theme={null}
keys = client.keys.list()

for key in keys:
    print(key.key_id, key.scope.value, key.alias)  # alias holds the description
```

### Revoke Key (fal keys revoke)

```python theme={null}
client.keys.revoke("key-id-to-revoke")
```

## Secrets Namespace

Store configuration values for builds and runtime without hardcoding them. Values are never returned when you list secrets.

### Set Secret (fal secrets set)

```python theme={null}
client.secrets.set("MY_API_TOKEN", "s3cr3t-value")

# Set secret in a specific environment
client.secrets.set("MY_API_TOKEN", "staging-value", environment_name="staging")
```

### List Secrets (fal secrets list)

```python theme={null}
secrets = client.secrets.list()
for secret in secrets:
    print(secret.name, secret.created_at, secret.environment_name)

# List secrets for a specific environment
staging_secrets = client.secrets.list(environment_name="staging")
```

### Unset Secret (fal secrets unset)

```python theme={null}
client.secrets.unset("MY_API_TOKEN")

# Unset secret in a specific environment
client.secrets.unset("MY_API_TOKEN", environment_name="staging")
```

## Environments Namespace

Create and manage environments to organize your applications and secrets across different stages (development, staging, production).

### Create Environment (fal environments create)

```python theme={null}
env = client.environments.create("staging", description="Staging environment")
print(f"Created: {env.name}")
```

### List Environments (fal environments list)

```python theme={null}
environments = client.environments.list()
for env in environments:
    print(env.name, env.description, env.is_default)
```

### Delete Environment (fal environments delete)

```python theme={null}
client.environments.delete("staging")
```

> [!WARNING]
>
> Deleting an environment removes all apps and secrets in that environment.

## Deploy (fal deploy)

Programmatic equivalent of `fal deploy`. If `app_ref` is omitted, discovery behavior matches the CLI (e.g., `pyproject.toml`).

```python theme={null}
# Auto-discover
client.deploy()

# Deploy from a file path
client.deploy("path/to/myfile.py")

# Deploy a specific class in a file
client.deploy("path/to/myfile.py::MyApp")

# Deploy by existing app name
client.deploy("my-app")

# With options mirroring CLI flags
client.deploy(
    app_ref="path/to/myfile.py::MyApp",
    app_name="myapp",
    auth="public",                        # "private" | "public"
    strategy="rolling",                   # "recreate" | "rolling"
    reset_scale=True,                     # use previous scaling if False
    environment_name="staging",           # target environment
    message="a1b2c3d fix cold-start",     # freeform message for this revision
    annotations={"GIT_SHA": "a1b2c3d4"},  # custom key/value pairs for this revision
)
```

The `message` and `annotations` are attached to the revision and can be used to identify it later in the dashboard or via the [revisions API](../../platform-apis/v1/serverless/apps/revisions.md). Annotation keys and values must be strings.

## Reference

> [card] [CLI Overview](https://fal.ai/docs/serverless/python/cli)
>
> CLI commands that mirror these methods

> [card] [API Reference](https://fal.ai/docs/reference/serverless-sdk/fal.api.md)
>
> Full method signatures and types
