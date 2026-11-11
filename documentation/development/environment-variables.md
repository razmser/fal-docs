> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Environment Variables and FAL_KEY

> Variables fal injects into every runner: FAL_KEY for calling other fal models, plus app identity, region, lifecycle state, and storage paths.

fal automatically injects several environment variables into every [runner](../deployment/runners.md) when it starts. These provide your app with authentication credentials, identity information, lifecycle state, and storage paths without any configuration. You access them via `os.getenv()` just like any other environment variable.

These platform variables are separate from your own [secrets](manage-secrets-securely.md), which you configure manually and which are also injected as environment variables. Platform variables are always present on every runner; secrets are specific to what you've set. Both are available at runtime (in `setup()` and endpoint handlers) but not during the image build stage.

```python theme={null}
import os
import fal

class MyApp(fal.App):
    def setup(self):
        app_name = os.getenv("FAL_APP_NAME")
        job_id = os.getenv("FAL_JOB_ID")
        print(f"Setting up {app_name} on runner {job_id}")
```

***

## Authentication

| Variable | Description |
| - | - |
| `FAL_KEY` | An auto-generated API key scoped to your account, formatted as `key_id:key_secret`. Use this to call other fal models from within your app without hardcoding credentials. |

The `FAL_KEY` variable is set automatically when your app runs with privileged access (the default for deployed apps). The fal client SDKs read it automatically, so you can call other fal endpoints without any additional configuration.

```python theme={null}
import fal
import fal_client
from pydantic import BaseModel

class Input(BaseModel):
    prompt: str

class MyApp(fal.App):
    @fal.endpoint("/")
    def run(self, input: Input):
        result = fal_client.subscribe("fal-ai/flux/schnell", arguments={
            "prompt": input.prompt
        })
        return result
```

> [!WARNING]
>
> `FAL_KEY` is scoped to **your account** (the app owner). Any fal API calls made with this key are billed to you, not to the end user calling your app. If your app chains calls to marketplace models, you pay for those calls on top of your own runner costs.

> [!NOTE]
>
> `FAL_KEY` is not available during the image build stage. It is only injected at runtime.

***

## App Identity

| Variable | Description |
| - | - |
| `FAL_APP_NAME` | Your application's full alias (e.g., `your-user/my-model`) |
| `FAL_APP_ID` | Your application's unique identifier |
| `FAL_USER_NICKNAME` | Your account username |
| `FAL_JOB_ID` | The current runner's unique job ID |
| `FAL_HOST` | The fal API host (e.g., `api.fal.ai`) |
| `FAL_REGION` | The region where the runner is executing (e.g., `us-east`) |

These are useful for structured logging, metrics tagging, and conditional logic. For example, you might log the runner ID and region with every request so you can correlate issues in the [dashboard](../serverless/observability/monitor-performance.md).

```python theme={null}
import os
import logging

import fal

class MyApp(fal.App):
    def setup(self):
        self.logger = logging.getLogger(os.getenv("FAL_APP_NAME", "app"))
        self.logger.info(
            f"Runner {os.getenv('FAL_JOB_ID')} starting in {os.getenv('FAL_REGION')}"
        )
```

***

## Lifecycle

| Variable | Values | Description |
| - | - | - |
| `FAL_RUNNER_STATE` | `SETUP`, `RUNNING`, `TERMINATING` | The current phase of the runner's lifecycle |

fal sets this variable at each stage of the [runner lifecycle](app-lifecycle.md): `SETUP` during `setup()`, `RUNNING` while serving requests, and `TERMINATING` after a shutdown signal is received. You can read it to make decisions in shared code paths, but for handling shutdown gracefully, use the [`handle_exit()` and `teardown()` methods](app-lifecycle.md) on your App class instead of raw signal handlers.

```python theme={null}
import os

import fal

class MyApp(fal.App):
    @fal.endpoint("/")
    def run(self) -> dict:
        state = os.getenv("FAL_RUNNER_STATE")
        return {"runner_state": state}

    def handle_exit(self):
        print("Shutdown signal received, stopping early")

    def teardown(self):
        print("Cleaning up resources")
```

***

## Storage

| Variable | Value | Description |
| - | - | - |
| `HF_HOME` | `/data/.cache/huggingface` | Hugging Face cache directory, persisted across runners via [/data storage](use-persistent-storage.md) |

fal sets `HF_HOME` to a path on the [persistent /data filesystem](use-persistent-storage.md) so that Hugging Face models are automatically cached across runner restarts. The first runner downloads the model weights, and subsequent runners reuse the cached files without re-downloading.

```python theme={null}
import fal
from transformers import AutoModel

class MyApp(fal.App):
    def setup(self):
        self.model = AutoModel.from_pretrained("bert-base-uncased")
```

***

## User Secrets

Any [secrets](manage-secrets-securely.md) you configure are also injected as environment variables alongside the platform variables listed above.

> [card] [Managing Secrets](manage-secrets-securely.md)
>
> Set, update, and scope secrets per environment via CLI, Dashboard, or Python SDK
