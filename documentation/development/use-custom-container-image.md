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

# Use a Custom Container Image

> Bring your own Dockerfile to fal for full control over system packages, CUDA versions, and base images.

When the [fal Runtime](fal-runtime.md) does not cover your needs, you can bring your own Dockerfile. This gives you full control over system packages, base images, CUDA versions, and non-Python dependencies while still using fal's endpoint system, scaling, and observability. Your Dockerfile is defined inline as a string in your app class, and fal handles the build, caching, and deployment.

If you are migrating an existing Docker-based server rather than building a new app, see [Deploy an Existing Server](migrate-external-docker-server.md) which covers the `exposed_port` pattern for zero-code migration. For an overview of when to use a Dockerfile versus pip requirements, see [Environment and Runtime](container-setup.md).

> [!NOTE]
>
> This page assumes your custom container still runs a `fal.App`. If your Dockerfile already starts an HTTP server and you want to deploy it without a `fal.App` wrapper, use [Direct Server Mode](migrate-external-docker-server.md#option-1-direct-server-mode).

## Basic Usage

Use `ContainerImage.from_dockerfile_str()` to define your Dockerfile inline, or `ContainerImage.from_dockerfile()` to reference a file on disk.

```python theme={null}
import fal
from fal.container import ContainerImage
from fal.toolkit import Image
from pydantic import BaseModel, Field

dockerfile_str = """
FROM pytorch/pytorch:2.1.0-cuda12.1-cudnn8-runtime
RUN apt-get update && apt-get install -y ffmpeg curl && rm -rf /var/lib/apt/lists/*
RUN pip install --no-cache-dir diffusers transformers accelerate
"""

class Input(BaseModel):
    prompt: str = Field(description="The prompt to generate an image from.")

class Output(BaseModel):
    image: Image

class MyApp(fal.App):
    machine_type = "GPU-A100"
    image = ContainerImage.from_dockerfile_str(dockerfile_str)

    def setup(self):
        import torch
        from diffusers import AutoPipelineForText2Image

        self.pipeline = AutoPipelineForText2Image.from_pretrained(
            "stabilityai/stable-diffusion-xl-base-1.0",
            torch_dtype=torch.float16,
            variant="fp16",
        ).to("cuda")

    @fal.endpoint("/")
    def generate(self, input: Input) -> Output:
        result = self.pipeline(prompt=input.prompt, num_inference_steps=30)
        return Output(image=Image.from_pil(result.images[0]))
```

> [!NOTE]
>
> For a complete walkthrough deploying a containerized app, see the [custom containers example](../../examples/deploy-models-with-custom-containers.md).

## fal Platform Requirements

fal injects a small runtime into your container to manage serialization, file uploads, and endpoint serving. Your Dockerfile must satisfy two requirements:

1. **`curl` must be installed.** fal uses it during runner startup.
2. **`pydantic`, `protobuf`, and `boto3` must be compatible with the fal SDK.** The easiest way to ensure this is to install the `fal` package itself as the last step in your Dockerfile, which brings in the correct versions. Alternatively, install these three packages last so they override any conflicting versions from your other dependencies.

```dockerfile theme={null}
FROM pytorch/pytorch:2.1.0-cuda12.1-cudnn8-runtime

RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*

# Install your application dependencies first
RUN pip install --no-cache-dir torch transformers diffusers

# Install fal last to ensure compatible versions of pydantic, protobuf, boto3
RUN pip install --no-cache-dir fal
```

## COPY and Build Context

> [!WARNING]
>
> **Experimental Feature** - COPY and ADD support for local files is currently experimental.

fal supports standard Docker `COPY` and `ADD` commands to include local files in your container builds. Paths are resolved relative to the current working directory (where you run `fal deploy`), or you can specify a custom `context_dir`.

```python theme={null}
dockerfile_str = """
FROM python:3.11
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY src/ ./src/
"""

class MyApp(fal.App):
    image = ContainerImage.from_dockerfile_str(dockerfile_str)
```

You can customize the build context directory for monorepos or shared code:

```python theme={null}
image = ContainerImage.from_dockerfile_str(
    dockerfile_str,
    context_dir=Path(__file__).parent / "docker_context"
)
```

### Supported COPY Patterns

```dockerfile theme={null}
# Entire directory
COPY . .

# Specific files and directories
COPY requirements.txt .
COPY src/ ./src/

# Glob patterns
COPY *.py /app/
COPY config/*.json /app/config/

# Multiple sources
COPY file1.py file2.py /app/
```

> [!NOTE]
>
> Files uploaded via `app_files` are already in the build context and will not be re-uploaded. fal uses content-based hashing to deduplicate files. With file-reference apps, the file containing your `fal.App` class is not part of the container image - it is pickled and sent at deploy time, so changes to your app code do not trigger a container rebuild. If you use a [`python_entry_point`](import-code.md#package-entry-points), install your package in the image and treat those package files as normal image build inputs.

### .dockerignore

Create a `.dockerignore` file in your project to exclude files from the build context, or define patterns in code:

```python theme={null}
# Inline patterns
image = ContainerImage.from_dockerfile_str(dockerfile_str, dockerignore=[
    ".git", "*.pyc", "__pycache__", ".env", "data/"
])

# Or reference an external file
image = ContainerImage.from_dockerfile_str(dockerfile_str,
    dockerignore_path=".dockerignore"
)

# Or use the add_dockerignore method
image = ContainerImage.from_dockerfile_str(dockerfile_str)
image.add_dockerignore(patterns=[".git", "*.pyc", ".env"])
image.add_dockerignore(path="docker/.dockerignore")
```

If no `.dockerignore` exists, fal uses sensible defaults that exclude common development artifacts.

### Alternative: Upload Large Files to CDN

For very large files or files that change independently from your container, you can upload them to fal's CDN and download them during the build:

```python theme={null}
from fal.toolkit import File

json_url = File.from_path("my-file.json", repository="cdn").url

dockerfile_str = f"""
FROM python:3.11-slim
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
RUN curl '{json_url}' > my-file.json
"""
```

You can also use Docker's `ADD` command to download directly from a URL:

```python theme={null}
req_url = File.from_path("requirements.txt", repository="cdn").url

dockerfile_str = f"""
FROM python:3.11-slim
ADD {req_url} /app/requirements.txt
WORKDIR /app
RUN pip install -r requirements.txt
"""
```

### Cache Behavior

Container images are cached based on Dockerfile content, build secrets, and the content hash of all context files. With file-reference apps, changing any local file except the app file itself triggers a rebuild because the app file containing your `fal.App` class is not baked into the image - it is pickled and sent at deploy time. With `python_entry_point`, the referenced package should be installed in the image, so changes to package files that are copied into the build context affect the image cache like any other Docker input.

To force a rebuild, pass `--no-cache`:

```bash theme={null}
fal deploy --no-cache path/to/my_app.py::MyApp
```

## Build Secrets

Use [BuildKit secret mounts](https://docs.docker.com/build/building/secrets/#secret-mounts) to pass secrets at build time without baking them into image layers. Secret values prefixed with `$` are resolved from your [fal secrets](manage-secrets-securely.md).

```python theme={null}
class MyApp(fal.App):
    image = ContainerImage.from_dockerfile_str("""
        FROM python:3.11
        RUN --mount=type=secret,id=pip_token \\
            pip install --extra-index-url \\
            https://$(cat /run/secrets/pip_token)@pypi.company.com/simple \\
            my-private-package
    """, secrets={"pip_token": "$MY_PIP_TOKEN"})
```

## Build Args

Use `build_args` to pass standard Docker build arguments. Build args are not secret and are visible in image layers.

```python theme={null}
class MyApp(fal.App):
    image = ContainerImage.from_dockerfile_str("""
        ARG PY_VERSION=3.11
        FROM python:${PY_VERSION}-slim
        ARG EXTRA_INDEX_URL
        RUN pip install --no-cache-dir --extra-index-url ${EXTRA_INDEX_URL} mypkg
    """, build_args={
        "PY_VERSION": "3.11",
        "EXTRA_INDEX_URL": "https://example.com/simple",
    })
```

## Using Private Registries

If your base image is hosted on a private registry (Docker Hub, Google Artifact Registry, AWS ECR, or Azure Container Registry), you provide credentials via the `registries` parameter. See [Private Registries](private-registries.md) for step-by-step authentication setup.

## ContainerImage Parameters

Both `ContainerImage.from_dockerfile_str()` and `ContainerImage.from_dockerfile()` accept the following parameters:

| Parameter | Type | Default | Description |
| - | - | - | - |
| `build_args` | `Dict[str, str]` | `{}` | Docker `--build-arg` values |
| `secrets` | `Dict[str, str]` | `{}` | BuildKit `--secret` values (not baked into layers) |
| `registries` | `Dict[str, Dict]` | `{}` | Private registry credentials |
| `context_dir` | `Path` | Current directory | Build context directory |
| `dockerignore` | `List[str]` | Default patterns | Patterns to exclude from context |
| `dockerignore_path` | `Path` | None | Path to `.dockerignore` file |
| `compression` | `str` | `"gzip"` | Image layer compression (`"gzip"`, `"zstd"`) |
| `force_compression` | `bool` | `False` | Force re-compression of all layers |

### build\_args

Docker build arguments passed as `--build-arg` during image build. Use to parameterize your Dockerfile.

```python theme={null}
image = ContainerImage.from_dockerfile_str("""
    ARG CUDA_VERSION=12.1
    FROM nvidia/cuda:${CUDA_VERSION}-runtime-ubuntu22.04
    RUN pip install torch
""", build_args={"CUDA_VERSION": "12.4"})
```

### secrets

Build-time secrets mounted via BuildKit `--secret`. Unlike `build_args`, secrets are not baked into image layers. Use for private package indexes or git tokens. Values prefixed with `$` are resolved from your [fal secrets](manage-secrets-securely.md) at build time.

```python theme={null}
image = ContainerImage.from_dockerfile_str("""
    FROM python:3.11
    RUN --mount=type=secret,id=pip_token \\
        pip install --extra-index-url \\
        https://$(cat /run/secrets/pip_token)@pypi.company.com/simple \\
        my-private-package
""", secrets={"pip_token": "$MY_PIP_TOKEN"})
```

### context\_dir

The build context directory. Files from this directory are available via `COPY` in your Dockerfile. Defaults to the current working directory.

```python theme={null}
image = ContainerImage.from_dockerfile("docker/Dockerfile",
    context_dir="./my-project"
)
```

### dockerignore / dockerignore\_path

Inline list of patterns to exclude from the build context, or a path to an external `.dockerignore` file. See the [.dockerignore section](#dockerignore) above for examples.

## Pre-Deploy Checklist

Before deploying, verify:

* All package versions are pinned
* `curl` is installed
* fal-compatible versions of pydantic, protobuf, and boto3 are installed (either via `pip install fal` or by installing them last)
* Container builds successfully with `fal run`

## Next Steps

For production-ready Dockerfile examples and optimization tips, see [Docker Templates and Best Practices](docker-templates.md). To optimize container image size and cold start times, see [Optimize Container Images](../serverless/optimizations/optimize-container-images.md).
