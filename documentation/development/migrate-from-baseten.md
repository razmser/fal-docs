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

# Migrate from Baseten: Move Truss Models to fal

> Move Baseten Truss models, custom servers, and Chains to fal. Map load and predict, config.yaml, GPUs, secrets, autoscaling, and CLI commands.

If you have been serving models on Baseten with [Truss](https://docs.baseten.co/development/model/overview), this guide maps Baseten concepts to their fal equivalents and shows how to convert your code. The core idea is similar: both platforms wrap a Python class with a startup hook and a request handler, then scale it behind a managed HTTP API. The main differences are that fal keeps configuration in the Python class rather than a separate `config.yaml`, and that a `fal.App` serves as many named endpoints as you declare rather than a single `predict` route.

For a broader overview of deploying existing Docker containers on fal (regardless of where they came from), see [Deploy an Existing Server](migrate-external-docker-server.md). If you are comparing fal to other platforms, see [Migrate from Replicate](migrate-from-replicate.md), [Migrate from Modal](migrate-from-modal.md), or [Migrate from RunPod](migrate-from-runpod.md).

## Concept Mapping

| Baseten | fal | Notes |
| - | - | - |
| `class Model:` in `model/model.py` | `class MyApp(fal.App)` | The unit you deploy |
| `def load(self)` | `def setup(self)` | Runs once per replica/runner at startup |
| `def predict(self, model_input)` | `@fal.endpoint("/")` | fal supports many named endpoints per app |
| `def preprocess()` / `def postprocess()` | Plain code in your endpoint | fal has no separate I/O hooks. See [Preprocess and Postprocess](#preprocess-and-postprocess) |
| (no shutdown hook) | `def teardown(self)` | Optional cleanup before a runner exits |
| `config.yaml` | Class attributes on `fal.App` | No separate config file |
| `requirements:` | `requirements = [...]` | Pip dependencies |
| `resources: {accelerator: H100}` | `machine_type = "GPU-H100"` | See [GPU Mapping](#gpu-mapping) |
| `secrets: {name: null}` + `self._secrets[...]` | `fal secrets set` + `os.getenv(...)` | Secrets arrive as environment variables |
| `base_image` / `docker_server` | `ContainerImage` or `exposed_port` | See [Custom Servers](#migration-path-2-custom-servers) |
| `model_cache` / `external_data` | `/data` persistent storage | Mounted automatically on every runner |
| `truss push` | `fal deploy` | CLI deployment |
| `truss push --watch` / `truss watch` | `fal run` | Validate an app before deploying |
| `truss predict` | `fal_client.subscribe(...)` | Or raw HTTP |
| `POST .../predict` | `fal_client.subscribe(...)` | Synchronous inference |
| `POST .../async_predict` + `webhook_endpoint` | `fal_client.submit(...)` + `webhook_url` | Queue-based inference |
| Chainlets + `chains.depends()` | [Multi-app routing](multi-app-routing.md) | Apps call each other over HTTP |
| Development / published / production deployments | [Environments](../deployment/manage-environments.md) | `main` by default; `--env staging` for others |

***

## Choosing a Path

Pick the path that matches how your Truss serves requests:

| If your Truss has | Use |
| - | - |
| A `Model` class in `model/model.py` | [Migration Path 1](#migration-path-1-a-truss-model) |
| A `docker_server` block in `config.yaml` | [Migration Path 2](#migration-path-2-custom-servers) |
| Chainlets defined with `truss_chains` | [Migration Path 3](#migration-path-3-chains) |

## Migration Checklist

The steps below are the full sequence for Path 1. Each one is expanded later in the guide.

1. **Create one Python file** next to your Truss. Everything in `config.yaml` and `model/model.py` ends up here.
2. **Convert the class.** `class Model:` becomes `class MyApp(fal.App)`, `load(self)` becomes `setup(self)`, and `predict(self, model_input)` becomes a method decorated with `@fal.endpoint("/")`. Drop `__init__` unless it does work beyond capturing `kwargs`.
3. **Move `config.yaml` keys to class attributes** using the [Concept Mapping](#concept-mapping) table. `requirements` carries over verbatim. `resources.accelerator` becomes `machine_type` via the [GPU Mapping](#gpu-mapping) table.
4. **Type the interface.** Replace the untyped `model_input` dict with a Pydantic `Input` model, and return a Pydantic `Output` model. Replace base64-encoded files with [`fal.toolkit`](working-with-files.md) types such as `Image` or `File`.
5. **Handle `python_version`.** If your Truss pinned it and your dependency pins depend on it, add a `pyproject.toml`. See [Python Version](#python-version).
6. **Recreate secrets.** For each key under `secrets:` in `config.yaml`, run `fal secrets set NAME=value`, and replace `self._secrets["name"]` with `os.getenv("NAME")`.
7. **Delete the cache configuration.** `model_cache` and `external_data` have no equivalent and are not needed. See [Model Weights and Caching](#model-weights-and-caching).
8. **Validate** with `fal run <file>::<Class>` before deploying. This runs `setup()` on a real runner. If you added a `pyproject.toml` in step 5, the argument is the app key instead: `fal run <app-key>`.
9. **Deploy** with `fal deploy <file>::<Class>`, or `fal deploy <app-key>` when using `pyproject.toml`.
10. **Repoint callers** from `https://model-{MODEL_ID}.api.baseten.co/...` to `fal_client.subscribe("your-username/your-app")`. See [Calling Your App](#calling-your-app).
11. **Re-baseline output tests.** Seeded outputs shift when the GPU changes. See the note under [GPU Mapping](#gpu-mapping).

***

## Migration Path 1: A Truss Model

This is the path almost every migration takes. A Truss is a directory containing `config.yaml` and `model/model.py`, and it collapses into a single fal application file.

The example below is an SDXL-Turbo text-to-image model. The first tab is the Truss as you would push it to Baseten, and the second is the same model as a `fal.App`.

> [tab] Baseten
>
> `config.yaml`:
>
> ```yaml theme={null}
> model_name: sdxl-turbo
> python_version: py311
> resources:
>   accelerator: T4
>   use_gpu: true
>   cpu: "4"
>   memory: 16Gi
> requirements:
>   - torch==2.4.0
>   - diffusers==0.30.3
>   - transformers==4.44.2
>   - accelerate==0.34.2
>   - safetensors==0.4.5
>   - pillow==10.4.0
> runtime:
>   predict_concurrency: 1
> secrets:
>   hf_access_token: null
> ```
>
> `model/model.py`:
>
> ```python theme={null}
> import base64
> import io
>
>
> class Model:
>     def __init__(self, **kwargs):
>         self._secrets = kwargs.get("secrets")
>         self.pipe = None
>
>     def load(self):
>         import torch
>         from diffusers import AutoPipelineForText2Image
>
>         self.pipe = AutoPipelineForText2Image.from_pretrained(
>             "stabilityai/sdxl-turbo",
>             torch_dtype=torch.float16,
>             variant="fp16",
>         ).to("cuda")
>
>     def predict(self, model_input):
>         import torch
>
>         prompt = model_input["prompt"]
>         seed = int(model_input.get("seed", 42))
>         steps = int(model_input.get("num_inference_steps", 4))
>
>         generator = torch.Generator(device="cuda").manual_seed(seed)
>         image = self.pipe(
>             prompt=prompt,
>             num_inference_steps=steps,
>             guidance_scale=0.0,
>             generator=generator,
>         ).images[0]
>
>         buffer = io.BytesIO()
>         image.save(buffer, format="PNG")
>         return {
>             "image_b64": base64.b64encode(buffer.getvalue()).decode("utf-8"),
>             "seed": seed,
>             "prompt": prompt,
>         }
> ```

> [tab] fal
>
> `sdxl_turbo.py`:
>
> ```python theme={null}
> import fal
> from fal.toolkit import Image
> from pydantic import BaseModel, Field
>
>
> class Input(BaseModel):
>     prompt: str = Field(description="The prompt to generate an image from.")
>     seed: int = Field(default=42, description="Seed for reproducible generation.")
>     num_inference_steps: int = Field(default=4, ge=1, le=10)
>
>
> class Output(BaseModel):
>     image: Image
>     seed: int
>     prompt: str
>
>
> class SDXLTurbo(fal.App, name="sdxl-turbo"):
>     machine_type = "GPU-A100"
>     requirements = [
>         "torch==2.4.0",
>         "diffusers==0.30.3",
>         "transformers==4.44.2",
>         "accelerate==0.34.2",
>         "safetensors==0.4.5",
>         "pillow==10.4.0",
>     ]
>
>     def setup(self):
>         import torch
>         from diffusers import AutoPipelineForText2Image
>
>         self.pipe = AutoPipelineForText2Image.from_pretrained(
>             "stabilityai/sdxl-turbo",
>             torch_dtype=torch.float16,
>             variant="fp16",
>         ).to("cuda")
>
>     @fal.endpoint("/")
>     def generate(self, input: Input) -> Output:
>         import torch
>
>         generator = torch.Generator(device="cuda").manual_seed(input.seed)
>         image = self.pipe(
>             prompt=input.prompt,
>             num_inference_steps=input.num_inference_steps,
>             guidance_scale=0.0,
>             generator=generator,
>         ).images[0]
>
>         return Output(
>             image=Image.from_pil(image),
>             seed=input.seed,
>             prompt=input.prompt,
>         )
> ```

Key differences in the fal version:

* **`config.yaml` disappeared.** `resources.accelerator` became `machine_type` and `requirements` became a class attribute. `python_version` is the one field with no class-attribute equivalent, and it is the first thing most Truss migrations trip over. See [Python Version](#python-version).
* **`load` became `setup`.** Same contract: it runs once per runner, and whatever you hang off `self` survives for that runner's lifetime.
* **`predict` became an endpoint.** `@fal.endpoint("/")` mounts the handler at the app root, which is what `fal_client` calls by default.
* **The untyped `model_input` dict became a Pydantic model.** Baseten hands you whatever JSON arrived. fal validates against `Input` first and rejects malformed requests before your code runs. The same models generate your app's OpenAPI spec, which powers the [Playground](../model-apis/playground.md) and the generated client code.
* **Base64 became a hosted file.** `fal.toolkit.Image.from_pil()` uploads the image to fal's CDN and returns a URL in the response, so large outputs never travel through your JSON payload. See [Working with Files](working-with-files.md).
* **`predict_concurrency: 1` is the fal default.** A runner handles one request at a time unless you raise [`max_multiplexing`](../deployment/scaling-configuration.md).
* **The declared secret went away.** `hf_access_token` was never read by this model. If yours does read one, see [Secrets](#secrets) below.

Note that `torch_dtype=torch.float16` and `.to("cuda")` are unchanged. The inference code itself almost never needs edits. A migration changes the wrapper around the model, not the model.

### Deploying

```bash theme={null}
# Baseten
truss push --promote

# fal
fal deploy sdxl_turbo.py::SDXLTurbo
```

Before deploying, validate the app the way you would iterate against a Baseten development deployment with `truss push --watch`:

```bash theme={null}
fal run sdxl_turbo.py::SDXLTurbo
```

`fal run` boots your app on a temporary runner, executing `setup()` and your endpoints exactly as production will. Import errors, missing dependencies, and model-loading failures surface here instead of as a production crashloop. It prints a URL you can `curl` and a generated playground UI.

### Calling Your App

> [tab] Baseten
>
> ```python theme={null}
> import os
> import requests
>
> model_id = "YOUR_MODEL_ID"
>
> response = requests.post(
>     f"https://model-{model_id}.api.baseten.co/environments/production/predict",
>     headers={"Authorization": f"Bearer {os.environ['BASETEN_API_KEY']}"},
>     json={"prompt": "an origami fox in a snowy forest", "seed": 42},
> )
> print(response.json())
> ```

> [tab] fal
>
> ```python theme={null}
> import fal_client
>
> result = fal_client.subscribe(
>     "your-username/sdxl-turbo",
>     arguments={"prompt": "an origami fox in a snowy forest", "seed": 42},
> )
> print(result["image"]["url"])
> ```

The addressing model differs. Baseten routes by opaque IDs, combining `model-{MODEL_ID}` with a deployment or environment segment, and treats names as cosmetic. On fal the name is the address. Your app is `your-username/sdxl-turbo`, and it stays that across every deploy. See [Calling Your Endpoints](calling-your-endpoints.md).

***

## Migration Path 2: Custom Servers

If your Truss uses `docker_server` to run vLLM, SGLang, Triton, or your own FastAPI service, you do not need a `fal.App` at all. fal's [Direct Server Mode](migrate-external-docker-server.md#option-1-direct-server-mode) forwards traffic straight to a port in your container.

> [tab] Baseten
>
> ```yaml theme={null}
> base_image:
>   image: your-registry/your-image:latest
> docker_server:
>   start_command: your-server --host 0.0.0.0 --port 8000
>   server_port: 8000
>   predict_endpoint: /predict
>   readiness_endpoint: /health
>   liveness_endpoint: /health
> ```

> [tab] fal
>
> ```toml theme={null}
> [tool.fal.apps.my-server]
> auth = "private"
> machine_type = "GPU-H100"
> exposed_port = 8000
> keep_alive = 300
>
> [tool.fal.apps.my-server.image]
> image = "your-registry/your-image:latest"
> cmd = ["your-server", "--host", "0.0.0.0", "--port", "8000"]
> ```

Two things change. `exposed_port` can be any valid port as long as your server binds the same one, so the port 8080 that Baseten reserves for its reverse proxy is free here. And `predict_endpoint` has no equivalent, because fal forwards all traffic to your container unchanged instead of mapping one inbound route onto one of your server's routes. Your existing paths stay exactly as they are. To get the dashboard Playground and analytics, expose `/openapi.json` from your server. With a `fal.App` the spec is generated from your Pydantic models instead.

Health checks also differ. Baseten polls the `readiness_endpoint` and `liveness_endpoint` you name in `config.yaml`. In Direct Server Mode, add a `[tool.fal.apps.my-server.health_check]` table with `path = "/health"` to `pyproject.toml`. On a `fal.App`, the [health check](add-health-check-endpoint.md) is an endpoint, declared as `@fal.endpoint("/health", health_check=fal.HealthCheck(timeout_seconds=10))`. See [Deploy an Existing Server](migrate-external-docker-server.md) for private registry credentials and the full `pyproject.toml` schema.

When you build from a Dockerfile rather than referencing a pushed image, the build context is the directory holding `pyproject.toml`, not the directory holding the Dockerfile. Write `COPY` paths relative to the project root:

```dockerfile theme={null}
COPY services/stats/server.py /opt/srv/server.py
```

A Truss has no equivalent step, because `docker_server` copies the Truss directory for you and there is no Dockerfile to write.

If you would rather keep a `fal.App` wrapper and only bring your own image, use [`ContainerImage`](use-custom-container-image.md) instead:

```python theme={null}
import fal
from fal.container import ContainerImage

class MyApp(fal.App):
    machine_type = "GPU-H100"
    image = ContainerImage.from_dockerfile_str("""
        FROM pytorch/pytorch:2.4.0-cuda12.1-cudnn9-runtime
        RUN apt-get update && apt-get install -y ffmpeg curl && rm -rf /var/lib/apt/lists/*
        RUN pip install --no-cache-dir diffusers transformers accelerate
    """)
```

This also replaces `system_packages` and `build_commands` from `config.yaml`.

***

## Migration Path 3: Chains

A Baseten Chain is several Chainlets, each with its own hardware, wired together with `chains.depends()` and deployed as one unit. fal has no single-deploy equivalent, and it does not need one: each step becomes an ordinary fal app, and a small CPU app routes between them.

> [tab] Baseten
>
> ```python theme={null}
> import truss_chains as chains
>
> class Transcribe(chains.ChainletBase):
>     remote_config = chains.RemoteConfig(
>         docker_image=chains.DockerImage(pip_requirements=["whisper"]),
>         compute=chains.Compute(gpu="L4"),
>     )
>
>     async def run_remote(self, audio_url: str) -> str:
>         return transcribe(audio_url)
>
>
> @chains.mark_entrypoint
> class Summarize(chains.ChainletBase):
>     remote_config = chains.RemoteConfig(compute=chains.Compute(gpu="H100"))
>
>     def __init__(self, transcribe=chains.depends(Transcribe)):
>         self._transcribe = transcribe
>
>     async def run_remote(self, audio_url: str) -> str:
>         text = await self._transcribe.run_remote(audio_url)
>         return summarize(text)
> ```

> [tab] fal
>
> ```python theme={null}
> import fal
> import fal_client
> from pydantic import BaseModel
>
>
> class AudioInput(BaseModel):
>     audio_url: str
>
>
> class Transcript(BaseModel):
>     text: str
>
>
> class Summary(BaseModel):
>     summary: str
>
>
> class Transcribe(fal.App, name="transcribe"):
>     machine_type = "GPU-A100"
>     requirements = ["whisper"]
>
>     @fal.endpoint("/")
>     def run(self, input: AudioInput) -> Transcript:
>         return Transcript(text=transcribe(input.audio_url))
>
>
> class Summarize(fal.App, name="summarize"):
>     machine_type = "GPU-H100"
>
>     @fal.endpoint("/")
>     def run(self, input: AudioInput) -> Summary:
>         transcript = fal_client.subscribe(
>             "your-username/transcribe",
>             arguments={"audio_url": input.audio_url},
>         )
>         return Summary(summary=summarize(transcript["text"]))
> ```

`FAL_KEY` is injected into every runner automatically, so an app can call another app without you configuring credentials. Deploy each app separately with `fal deploy`.

Chains already require typed inputs and outputs, so this part of the port is mechanical. A `run_remote` returning a bare `dict` is rejected at push time with an `IO_TYPE_ERROR`, which means your Chain already has Pydantic models, and those models carry over to `fal.App` endpoints unchanged.

There are trade-offs in both directions. Chains give you one deploy, calls between steps that stay inside the platform, and a dependency graph it understands. Separate fal apps version, scale, and roll back independently, and any other app or client can call them directly, since each one is a normal endpoint rather than an internal node.

The cost of that independence is a network hop per step. Every call from one fal app to another is an HTTP round trip through the gateway, where a Chainlet-to-Chainlet call is not. On a trivial two-step CPU pipeline the difference is visible: roughly 0.2s warm as a Chain against roughly 0.8s warm as two apps. Using `fal_client.run` instead of `subscribe` for the internal call does not close the gap, because the cost is the round trip rather than the queue. For steps doing real GPU work the overhead is a rounding error, but for chains of small, fast steps it is worth keeping the pipeline inside one app and calling plain Python methods between stages.

See [Multi-App Routing](multi-app-routing.md) for routing by input size, A/B tests, and fallbacks.

***

## Key Differences

### Python Version

Almost every Truss sets `python_version` in `config.yaml`, and it is the one setting that does not become a `fal.App` class attribute. If you drop it and your pins were chosen for an older interpreter, the environment build fails on dependency resolution rather than on anything in your code:

```
× No solution found when resolving dependencies:
╰─▶ Because torch==2.4.0 has no wheels with a matching Python ABI tag
    (e.g., `cp314`) and you require torch==2.4.0, we can conclude that your
    requirements are unsatisfiable.
    hint: You require CPython 3.14 (`cp314`), but we only found wheels for
    `torch` (v2.4.0) with the following Python ABI tags: `cp38`, `cp39`,
    `cp310`, `cp311`, `cp312`
```

Set it in [`pyproject.toml`](../../api-reference/python-sdk/pyproject-toml.md) rather than in the class:

```toml theme={null}
[tool.fal.apps.sdxl-turbo]
ref = "sdxl_turbo.py::SDXLTurbo"
auth = "private"
python_version = "3.11"
machine_type = "GPU-A100"
```

Then deploy by app key instead of by file reference:

```bash theme={null}
fal deploy sdxl-turbo
```

> [!WARNING]
>
> **Your local interpreter must match `python_version`.** fal serializes your app code from the machine you deploy from, so a mismatch is rejected up front:
>
> ```
> ✘ Local Python 3.14 differs from the app's python_version=3.11.
>   Run from a Python 3.11 interpreter, or set python_version='3.14' on the app.
> ```
>
> Install the fal CLI into a virtualenv on the version you are targeting and deploy from there. This has no analogue on Baseten, where `truss push` ships a directory and the interpreter is purely a server-side concern.

The alternative is to leave `python_version` unset and relax the pins your Truss inherited, letting the resolver pick versions that have wheels for your local Python version. That is the better choice for a new app. Pin the version when you are migrating and want to reproduce a known-good environment exactly.

### GPU Mapping

Baseten names accelerators by chip (`accelerator: H100`) and sizes CPU and memory separately. fal bundles each GPU with a fixed CPU and RAM allocation, so `machine_type` is the only knob.

| Baseten `accelerator` | Closest fal `machine_type` | fal VRAM |
| - | - | - |
| `T4` (16 GB) | `GPU-A100` | 40 GB |
| `L4` (24 GB) | `GPU-A100` | 40 GB |
| `A10G` (24 GB) | `GPU-A100` | 40 GB |
| `A100` / `A100_40GB` | `GPU-A100` | 40 GB |
| `H100` / `H100_40GB` | `GPU-H100` | 80 GB |
| `RTX_PRO_6000` (96 GB) | `GPU-RTXPRO6000` | 96 GB |
| `H200` (141 GB) | `GPU-H200` | 141 GB |
| `B200` (192 GB) | `GPU-B200` | 192 GB |
| CPU-only instances (`1x2` through `16x64`) | `XS`, `S`, `M`, `L`, `XL` | -- |

fal's smallest GPU is the 40 GB A100, so models sized for a T4, L4, or A10G land on more VRAM than they had. For an accelerator not listed above, pick the smallest fal GPU your model fits in. For multi-GPU, the equivalent of Baseten's `instance_type: "H100:8x80"` is `num_gpus`:

```python theme={null}
import fal

class MyApp(fal.App):
    machine_type = "GPU-H100"
    num_gpus = 8
```

You can also list several machine types, tried in order, to widen the pool of machines your app can land on:

```python theme={null}
import fal

class MyApp(fal.App):
    machine_type = ["GPU-H100", "GPU-A100"]
```

See [Machine Types](../deployment/machine-types.md) for the full table.

> [!NOTE]
>
> **Expect your outputs to shift slightly.** Because the GPU changes, a seeded generation that was bit-identical on Baseten will not reproduce byte-for-byte on fal. Running the example above on both platforms with the same prompt and seed gives images that are each perfectly reproducible on their own platform but differ from one another by a mean of 0.6/255 per channel (PSNR 43 dB). They are visually indistinguishable, but not the same bytes.
>
> If you have golden-image tests that assert on a hash, they will fail after migration. Assert on a perceptual distance threshold instead.

### Autoscaling

The settings line up almost one for one. Baseten calls its units replicas; fal calls them [runners](../deployment/runners.md).

| Baseten setting | Default | fal parameter | Default |
| - | - | - | - |
| Min replica | `0` | `min_concurrency` | `0` |
| Max replica | `1` | `max_concurrency` | unset |
| Concurrency target | `1` | `max_multiplexing` | `1` |
| Scale-down delay | `900s` | `keep_alive` | `60s` |
| Autoscaling window | `60s` | `scaling_delay` | `0s` |
| Target utilization | `70%` | `concurrency_buffer` / `concurrency_buffer_perc` | unset |

**`min_concurrency` and `max_concurrency` are runner counts, not request counts, despite the names.** They are the direct equivalents of Baseten's min and max replica. The per-runner request count, which Baseten calls the concurrency target, is `max_multiplexing`.

Two defaults are worth checking against your Baseten config before your first deploy. fal keeps idle runners for 60 seconds where Baseten waits 900, so a sporadically used app will cold-start far more often unless you raise `keep_alive`. And fal scales up immediately (`scaling_delay = 0`) rather than averaging over a 60-second window, which reacts faster but is more sensitive to brief spikes.

Where Baseten reserves headroom as a percentage of a replica's capacity, fal reserves it as a number of spare runners. `concurrency_buffer` sets a floor, `concurrency_buffer_perc` scales with request volume, and the effective buffer is whichever is larger.

```python theme={null}
import fal

class MyApp(fal.App):
    machine_type = "GPU-H100"
    keep_alive = 300
    min_concurrency = 1
    max_concurrency = 10
    max_multiplexing = 1
```

As with Baseten's autoscaling panel, you can change these without redeploying:

```bash theme={null}
fal apps scale my-app --min-concurrency 2 --max-concurrency 20 --keep-alive 600
```

One behavior to know: runtime-tunable values set via CLI or dashboard **persist across deploys** and override what is in your code. `fal deploy --reset-scale` discards them. See [Updating Your Configuration](../deployment/scaling-configuration.md).

### Queue-Based Inference

Baseten's `/async_predict` and fal's queue solve the same problem, and the fal version is the default path rather than a separate endpoint.

> [tab] Baseten
>
> ```python theme={null}
> resp = requests.post(
>     f"https://model-{model_id}.api.baseten.co/production/async_predict",
>     headers={"Authorization": f"Bearer {api_key}"},
>     json={
>         "model_input": {"prompt": "hello world"},
>         "webhook_endpoint": "https://your-server.com/webhook",
>         "priority": 0,
>     },
> )
> request_id = resp.json()["request_id"]
> ```

> [tab] fal
>
> ```python theme={null}
> import fal_client
>
> handler = fal_client.submit(
>     "your-username/sdxl-turbo",
>     arguments={"prompt": "hello world"},
>     webhook_url="https://your-server.com/webhook",
> )
> request_id = handler.request_id
> ```

Three practical differences:

* **No separate route.** `subscribe` and `submit` both go through the queue; `subscribe` just polls for you. Baseten needs you to choose `/predict` or `/async_predict` up front.
* **The input is not wrapped.** Baseten nests your payload under `model_input`; fal sends it at the top level.
* **Status polling looks the same.** `handler.status()` and `handler.get()` replace `GET /async_request/{request_id}`.

See [Queue](../model-apis/inference/queue.md) and [Webhooks](../model-apis/inference/webhooks.md).

### Secrets

Baseten requires secrets to be declared in `config.yaml` with a `null` placeholder and reads them from a dict passed to `__init__`. On fal, secrets arrive as environment variables and no declaration is required.

> [tab] Baseten
>
> ```yaml theme={null}
> secrets:
>   hf_access_token: null
> ```
>
> ```python theme={null}
> class Model:
>     def __init__(self, **kwargs):
>         self._secrets = kwargs["secrets"]
>
>     def load(self):
>         import huggingface_hub
>         huggingface_hub.login(token=self._secrets["hf_access_token"])
> ```

> [tab] fal
>
> ```bash theme={null}
> fal secrets set HF_TOKEN=hf_abc123
> ```
>
> ```python theme={null}
> import os
> import fal
>
> class MyApp(fal.App):
>     def setup(self):
>         import huggingface_hub
>         huggingface_hub.login(token=os.getenv("HF_TOKEN"))
> ```

The one Baseten habit worth keeping is the explicit declaration. fal's optional `secrets` allowlist limits a runner to the secrets you name, rather than injecting everything in the environment:

```python theme={null}
import fal

class MyApp(fal.App):
    secrets = ["HF_TOKEN"]
```

Note that a Truss reading secrets from a custom Docker image reads files at `/secrets/{name}`; on fal it is `os.getenv` in both cases. Secrets are injected at runner startup, so updating one requires a redeploy to take effect everywhere. See [Secrets](manage-secrets-securely.md).

### Model Weights and Caching

Baseten offers `model_cache` and `external_data` in `config.yaml` so that weights are fetched ahead of time rather than on every cold start. fal gives every runner a `/data` volume instead. It is a persistent filesystem shared across all your apps and runners, and it requires no configuration.

```python theme={null}
import os
import fal

class MyApp(fal.App):
    def setup(self):
        weights = "/data/models/my-model.safetensors"
        if not os.path.exists(weights):
            download_to(weights)
        self.model = load(weights)
```

For Hugging Face libraries this is already done for you. fal sets `HF_HOME` to `/data/.cache/huggingface`, so `from_pretrained` calls in `transformers`, `diffusers`, and `huggingface_hub` cache to persistent storage automatically. This is why the SDXL-Turbo example above needs no cache configuration. The first runner downloads the weights, and every later runner reads them from `/data`.

When several runners might write the same path at once, write to a temporary file in `/data` and `os.rename` it into place. See [Persistent Storage](use-persistent-storage.md) and [Downloading Models and Files](download-model-weights-and-files.md).

### Preprocess and Postprocess

Baseten's `preprocess` and `postprocess` run in separate threads, outside `predict_concurrency`, so that downloading an input image does not occupy a GPU slot. fal has no equivalent hooks. Two approaches replace them:

* **Do the I/O inline.** Raise [`max_multiplexing`](../deployment/scaling-configuration.md) so a runner can overlap the I/O of one request with the GPU work of another. Your handlers must be safe to run concurrently.
* **Split the app.** A CPU app fetches and normalizes inputs, then calls the GPU app. This is [Multi-App Routing](multi-app-routing.md), and it keeps the expensive machine doing only GPU work.

### Streaming and WebSockets

A Truss streams by returning a `StreamingResponse` from `predict` or `postprocess`. On fal, streaming is a distinct endpoint that returns SSE, so an app can offer both a buffered and a streaming route:

```python theme={null}
import fal
from fastapi.responses import StreamingResponse

class MyApp(fal.App):
    @fal.endpoint("/")
    def generate(self, input: Input) -> Output:
        ...

    @fal.endpoint("/stream")
    def generate_stream(self, input: Input) -> StreamingResponse:
        ...
```

Baseten's WebSocket support (`runtime.transport` set to websocket, or `is_websocket_endpoint`) maps to fal's [Realtime Endpoints](realtime.md). `@fal.realtime("/realtime")` uses a binary msgpack protocol built for back-to-back interactive requests; if you want the raw connection instead, use `@fal.endpoint("/ws", is_websocket=True)`. See [Streaming](streaming.md).

### Deployments and Environments

Baseten distinguishes development deployments (mutable, live-reloading, created by `truss push --watch`) from published ones, and promotes a deployment into the `production` environment.

fal's model is flatter. There is no development-versus-published distinction, since `fal run` covers iteration and `fal deploy` publishes. Environments are namespaces rather than promotion targets. Each app belongs to exactly one, and you deploy the same code to each independently rather than promoting between them.

```bash theme={null}
fal environments create staging --description "Staging environment"
fal deploy sdxl_turbo.py::SDXLTurbo --env staging
```

Rolling back does not require a promotion either. fal keeps previous revisions, and you can [roll back](../deployment/rollbacks.md) to one directly. See [Manage Environments](../deployment/manage-environments.md) and [Deploy to Production](../deployment/deploy-to-production.md).

### CLI Mapping

| Baseten | fal |
| - | - |
| `truss init my-model` | `fal create app` |
| `truss push` | `fal deploy app.py::MyApp` |
| `truss push --watch` / `truss watch` | `fal run app.py::MyApp` |
| `truss push --environment staging` | `fal deploy app.py::MyApp --env staging` |
| `truss predict` | `fal_client.subscribe(...)` |
| `truss model-logs` | [`fal runners logs <runner-id>`](../../api-reference/cli/runners.md) or the [dashboard](https://fal.ai/dashboard/logs) |
| Autoscaling panel in dashboard | `fal apps scale` |
| Workspace secrets settings | `fal secrets set` |
| `truss chains push` | `fal deploy` per app |

## Troubleshooting

Failures specific to arriving from a Truss, with the message each one produces.

| Symptom | Cause | Fix |
| - | - | - |
| `No solution found when resolving dependencies` naming a `cp314` ABI tag | Your Truss pinned an older `python_version`, and your dependency pins have no wheel for your local Python version. When `python_version` is not set, fal uses your local Python version | Pin `python_version` in `pyproject.toml`. See [Python Version](#python-version) |
| `Local Python X differs from the app's python_version=Y` | fal serializes your app from the deploying machine, so the interpreters must match | Install the fal CLI in a virtualenv on version `Y` and deploy from it |
| `os.getenv("NAME")` returns `None` for a secret that worked on Baseten | Baseten keys secrets by the name in `config.yaml`; fal reads the environment variable name you set | Confirm with `fal secrets list`, and check any `secrets = [...]` allowlist on the class |
| Golden-image tests fail on hash comparison | The GPU changed, so seeded outputs are no longer byte-identical | Compare with a perceptual threshold. See [GPU Mapping](#gpu-mapping) |
| The app cold-starts far more often than on Baseten | `keep_alive` defaults to 60s against Baseten's 900s scale-down delay | Raise `keep_alive`. See [Autoscaling](#autoscaling) |
| Requests queue instead of scaling out | `max_concurrency` caps total runners, as Baseten's max replica does | Raise `max_concurrency`, or `max_multiplexing` if one runner can serve more |
| `"/server.py": not found. Please check if the files exist in the context.` | fal's Docker build context is the `pyproject.toml` directory, not the Dockerfile's directory | Write `COPY` paths relative to the project root |
| `IO_TYPE_ERROR` on `truss chains push` | Chains reject untyped `dict` inputs and outputs | Declare Pydantic models. They port to `fal.App` unchanged |

For failures during `setup()` or a request, read the runner logs with [`fal runners logs <runner-id>`](../../api-reference/cli/runners.md) or the [dashboard](https://fal.ai/dashboard/logs). Reproduce locally with `fal run` before redeploying.

## Next Steps

Once you have migrated your app, the [App Lifecycle](app-lifecycle.md) page explains how the full lifecycle works on fal, from code serialization to runner shutdown. For the closest analogue to Baseten's cold-start settings, see [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md). For scaling configuration, see [Scale Your Application](../deployment/scale-your-application.md). For monitoring your deployed app, see [App Analytics](../serverless/observability/app-analytics.md).
