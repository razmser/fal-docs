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

# Migrate a Cog Model or Custom Container to fal

> Run an existing Cog image or Dockerfile on fal Serverless. Learn what changes, how long a migration takes, and how per-second GPU billing works.

fal Serverless runs custom containers. You can build your app from your own Dockerfile, or you can run an existing container image from Docker Hub or a private registry. A model that you packaged with [Cog](https://github.com/replicate/cog) is a container image, so it can run on fal.

This guide shows three migration paths for a Cog model:

1. Run the existing Cog image without code changes.
2. Build from a Dockerfile, such as the Dockerfile that Cog generates.
3. Convert the Cog `Predictor` class to a `fal.App`.

Each path deploys with the `fal` CLI and gives you autoscaling, logs, and [app analytics](../serverless/observability/app-analytics.md). For other Docker servers that do not use Cog, see [Migrate an External Docker Server](migrate-external-docker-server.md).

## Choose a Migration Path

| Path | What you change | Typical time | Use it when |
| - | - | - | - |
| [1. Run the Cog image](#path-1-run-the-existing-cog-image) | A `pyproject.toml` entry only | Under an hour | You want the model live on fal quickly and keep the Cog API |
| [2. Build from a Dockerfile](#path-2-build-from-a-dockerfile) | The Dockerfile and a small `fal.App` | A few hours | You need system packages or a specific CUDA version |
| [3. Convert to `fal.App`](#path-3-convert-the-predictor-to-a-fal-app) | `predict.py` becomes a `fal.App` class | Half a day to a day | You want typed inputs, CDN file outputs, and the full Playground |

The time includes the first image build and a test deployment. Large model weights add download time to the first build and the first runner start.

## Before You Start

1. Install the `fal` CLI and log in. See [Installation](getting-started/installation.md).
2. Make sure your account has Serverless access. Request access on the [Serverless get-started page](https://fal.ai/dashboard/serverless-get-started).
3. Choose a GPU. See [Machine Types](../deployment/machine-types.md) for the list, for example `GPU-H100` or `GPU-B200`.

## Path 1: Run the Existing Cog Image

A Cog image contains its own HTTP server. On fal, an image-only app starts the container and sends requests to the port that you expose. You do not write a `fal.App` class.

### Build and Push the Image

Build the image with Cog and push it to a registry that fal can pull from:

```bash theme={null}
cog build -t docker.io/my-org/my-cog-model:v1
docker push docker.io/my-org/my-cog-model:v1
```

If the registry is private, configure credentials. See [Private Docker Registries](private-registries.md) for Docker Hub, Google Artifact Registry, Amazon ECR, and Azure Container Registry.

### Configure the App

Add the app to `pyproject.toml`. Do not set `ref` or `python_entry_point`. Without them, fal treats the container as an image-only app and does not load the fal Python runtime.

```toml theme={null}
[tool.fal.apps.my-cog-model]
auth = "private"
machine_type = "GPU-H100"
exposed_port = 5000
keep_alive = 300

[tool.fal.apps.my-cog-model.image]
image = "docker.io/my-org/my-cog-model:v1"

[tool.fal.apps.my-cog-model.health_check]
path = "/health-check"
start_period_seconds = 600
```

The Cog image already defines the command that starts its server, so you do not set `cmd`. To override it, add `cmd` under `[tool.fal.apps.my-cog-model.image]`. See the [pyproject.toml reference](../../api-reference/python-sdk/pyproject-toml.md#container-image).

### Test and Deploy

Run an ephemeral deployment to test the image:

```bash theme={null}
fal run my-cog-model --auth private
```

> [!WARNING]
>
> `fal run` ignores the `auth` value in `pyproject.toml` and defaults to `public`. Pass `--auth private` unless you want the test deployment to be public. `fal deploy` uses the configured value.

Deploy the app to a persistent URL:

```bash theme={null}
fal deploy my-cog-model
```

### Call the App

fal forwards each request to the container without changes. The Cog API stays the same, so send the Cog request body to the `/predictions` path under your endpoint ID:

```bash theme={null}
curl -X POST "https://queue.fal.run/<your-username>/my-cog-model/predictions" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{"input": {"prompt": "a sunset over mountains"}}'
```

The queue returns a `status_url` and a `response_url`. Poll the status URL, then fetch the result from the response URL. See [Calling Your Endpoints](calling-your-endpoints.md) for queue, webhook, and streaming flows.

The Cog server returns files in the format that Cog uses, such as data URIs or paths. They do not go to the [fal CDN](../model-apis/fal-cdn.md). To return CDN URLs, use path 3.

## Path 2: Build From a Dockerfile

Use this path when the model needs system packages, a specific CUDA version, or build steps that a `requirements` list cannot express. fal builds the image from your Dockerfile and caches the result.

### Get a Dockerfile

If you already have a Dockerfile, use it. If you only have a `cog.yaml`, print the Dockerfile that Cog generates:

```bash theme={null}
cog debug > Dockerfile
```

Then edit the generated Dockerfile:

1. Remove the `COPY . /src`, `EXPOSE`, and `CMD` lines at the end. fal starts the app.
2. Remove the Cog wheel installation (`cog-*.whl`). fal does not use the Cog runtime.
3. Replace the Cog requirements step with your pip packages.
4. Make sure that `curl` is installed. fal uses it when a runner starts.
5. Install `fal` as the last step. This gives compatible versions of `pydantic`, `protobuf`, and `boto3`.

> [!NOTE]
>
> `cog debug` is a hidden debugging command, and Cog does not guarantee its output format. The generated Dockerfile can change between Cog versions.

### Reference the Dockerfile From a `fal.App`

```python theme={null}
import fal
from fal.container import ContainerImage
from fal.toolkit import Image
from pydantic import BaseModel, Field


class Input(BaseModel):
    prompt: str = Field(description="Text prompt")


class Output(BaseModel):
    image: Image


class MyApp(fal.App):
    machine_type = "GPU-H100"
    image = ContainerImage.from_dockerfile("Dockerfile")

    def setup(self):
        import torch
        from diffusers import StableDiffusionXLPipeline

        self.pipe = StableDiffusionXLPipeline.from_pretrained(
            "stabilityai/stable-diffusion-xl-base-1.0",
            torch_dtype=torch.float16,
        ).to("cuda")

    @fal.endpoint("/")
    def predict(self, input: Input) -> Output:
        image = self.pipe(input.prompt).images[0]
        return Output(image=Image.from_pil(image))
```

`ContainerImage.from_dockerfile()` reads a file from disk. To keep the Dockerfile in your Python file, use `ContainerImage.from_dockerfile_str()`. Both accept `build_args`, `secrets`, `registries`, and a build `context_dir` for `COPY`. See [Use a Custom Container Image](use-custom-container-image.md) for all options and the build cache rules.

You can also keep the Dockerfile and its settings in `pyproject.toml`. Set `dockerfile = "Dockerfile"` under `[tool.fal.apps.<app-name>.image]`. See the [pyproject.toml reference](../../api-reference/python-sdk/pyproject-toml.md#container-image).

## Path 3: Convert the Predictor to a `fal.App`

The Cog `Predictor` class maps directly to a `fal.App`. The `setup()` method stays the same. The `predict()` method becomes an `@fal.endpoint` method with Pydantic input and output models.

| Cog | fal | Notes |
| - | - | - |
| `cog.yaml` | `requirements = [...]` or `ContainerImage` | Environment definition |
| `class Predictor(BasePredictor)` | `class MyApp(fal.App)` | App class |
| `def setup(self)` | `def setup(self)` | One-time model load per runner |
| `def predict(self, ...)` | `@fal.endpoint("/")` | Request handler |
| `cog.Path` (file output) | `fal.toolkit.Image` or `fal.toolkit.File` | fal uploads output files to the CDN |
| `Input(...)` type hints | Pydantic `BaseModel` | Standard Pydantic validation |
| `cog push` | `fal deploy` | One CLI command |
| Webhooks | `webhook_url` parameter | Both deliver results to a webhook |

> [tab] Cog
>
> ```yaml theme={null}
> # cog.yaml
> build:
>   python_version: "3.11"
>   python_packages:
>     - torch==2.1.0
>     - diffusers==0.30.0
>     - transformers
>     - accelerate
>   gpu: true
> predict: "predict.py:Predictor"
> ```
>
> ```python theme={null}
> # predict.py
> from cog import BasePredictor, Input, Path
> import torch
> from diffusers import StableDiffusionXLPipeline
>
> class Predictor(BasePredictor):
>     def setup(self):
>         self.pipe = StableDiffusionXLPipeline.from_pretrained(
>             "stabilityai/stable-diffusion-xl-base-1.0",
>             torch_dtype=torch.float16,
>         ).to("cuda")
>
>     def predict(self, prompt: str = Input(description="Text prompt")) -> Path:
>         image = self.pipe(prompt).images[0]
>         output_path = "/tmp/output.png"
>         image.save(output_path)
>         return Path(output_path)
> ```

> [tab] fal
>
> ```python theme={null}
> import fal
> from pydantic import BaseModel, Field
> from fal.toolkit import Image
>
> class Input(BaseModel):
>     prompt: str = Field(description="Text prompt")
>
> class Output(BaseModel):
>     image: Image
>
> class MyApp(fal.App):
>     machine_type = "GPU-H100"
>     requirements = ["torch==2.1.0", "diffusers==0.30.0", "transformers", "accelerate"]
>
>     def setup(self):
>         import torch
>         from diffusers import StableDiffusionXLPipeline
>
>         self.pipe = StableDiffusionXLPipeline.from_pretrained(
>             "stabilityai/stable-diffusion-xl-base-1.0",
>             torch_dtype=torch.float16,
>         ).to("cuda")
>
>     @fal.endpoint("/")
>     def predict(self, input: Input) -> Output:
>         image = self.pipe(input.prompt).images[0]
>         return Output(image=Image.from_pil(image))
> ```

In the fal version, class attributes replace `cog.yaml`. `fal.toolkit.Image` replaces `cog.Path` and uploads the image to the [fal CDN](../model-apis/fal-cdn.md), so the response contains a URL. The imports are inside `setup()` so that they run on the runner, not on your machine. See [Serialization and Build](app-lifecycle.md#serialization-and-build) for the reason.

Test the app on a temporary runner, then deploy it:

```bash theme={null}
fal run my_app.py::MyApp
fal deploy my_app.py::MyApp
```

`fal run` executes `setup()` and your endpoints as production does. Import errors and model-load failures show in your terminal, not as a failed production deployment.

Call the deployed app:

```python theme={null}
import fal_client

result = fal_client.subscribe("<your-username>/my-app", arguments={
    "prompt": "a sunset over mountains"
})
print(result["image"]["url"])
```

## What Changes After the Migration

| Area | Cog setup | On fal |
| - | - | - |
| Build | `cog build` and `cog push` | fal builds from your Dockerfile or `requirements`, or pulls your existing image |
| Configuration | `cog.yaml` | `fal.App` class attributes or `pyproject.toml` |
| API shape | `POST /predictions` with an `input` object | Path 1 keeps it. Paths 2 and 3 use your Pydantic models |
| Output files | Data URIs or paths from the Cog server | Path 1 keeps them. Paths 2 and 3 return fal CDN URLs |
| Scaling | Set by the platform | You set `keep_alive`, `min_concurrency`, and `max_concurrency`. See [Scale Your Application](../deployment/scale-your-application.md) |
| Secrets | Platform environment variables | [fal secrets](manage-secrets-securely.md) |
| Auth | Platform API token | fal API key (`Authorization: Key $FAL_KEY`) |

## How Billing Works on fal

fal bills Serverless apps per second of runner time, at the rate for the machine type. A runner is a container on a GPU. You pay while a runner is alive, not per prediction.

| Runner state | Billed |
| - | - |
| PENDING (waiting for hardware) | No |
| DOCKER\_PULL (pulling your image) | No |
| SETUP (running `setup()` or the server start) | Yes |
| IDLE (ready, including `keep_alive` time) | Yes |
| RUNNING (processing requests) | Yes |
| DRAINING and TERMINATING | Yes |

fal does not charge for requests that fail with an HTTP 5xx status. fal bills a multi-GPU runner as the GPU count multiplied by the duration. See [Serverless Pricing](../serverless/pricing.md) for the full rules, and [fal.ai/pricing](https://fal.ai/pricing) for the hourly rate of each GPU.

Three settings decide most of the cost:

* **`keep_alive`** keeps an idle runner warm for a number of seconds after the last request. A longer value reduces cold starts and adds IDLE time.
* **`min_concurrency`** keeps a minimum number of runners alive at all times. Set it to `0` to scale to zero, so that an app with no traffic has no compute charge.
* **`machine_type`** sets the per-second rate. Use the smallest GPU that fits the model.

To estimate the cost, add up the seconds that your runners are alive, not the number of predictions. [App Analytics](../serverless/observability/app-analytics.md) shows runner time and cost for each app.

## Test Your Deployment

Open your app from the [Apps dashboard](https://fal.ai/dashboard/apps). Follow [Testing Your Own Apps](../model-apis/playground.md#your-own-apps) to test an endpoint. Use the app's Logs and Requests pages to inspect recorded requests.

## Next Steps

* [App Lifecycle](app-lifecycle.md) explains each stage, from code serialization to runner shutdown.
* [Use a Custom Container Image](use-custom-container-image.md) covers every Dockerfile option.
* [Optimize Container Images](../serverless/optimizations/optimize-container-images.md) reduces build and start times.
* [Scale Your Application](../deployment/scale-your-application.md) explains the scaling settings.
