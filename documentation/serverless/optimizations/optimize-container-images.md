> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../quickstart.md) and the [Model APIs overview](../../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Optimize Container Images

> Container optimization is key to achieving faster cold starts, reducing deployment sizes, and improving overall application performance. This guide covers Dockerfile optimization techniques, layer caching strategies, multi-stage builds, and build performance improvements to help you create efficient containers for your fal.ai applications.

## Overview

Under the hood, we use [buildkit](https://docs.docker.com/build/buildkit) (or [docker buildx](https://docs.docker.com/reference/cli/docker/buildx)) to build docker images. This allows us to take advantage of advanced caching mechanisms to improve build times and reduce resource consumption. In this guide, we'll provide some guidelines for creating cache-efficient Dockerfiles.

> [!NOTE]
>
> **Note**
>
> Ensure you have Docker Buildx and BuildKit enabled in your Docker environment
> if you want to test your containers locally. Otherwise, you don't need to
> worry about it. fal platform takes care of it for you when you deploy your
> application using container support.

Check out the [Docker buildx documentation](https://github.com/docker/buildx) for more information.

## General Guidelines

> [!NOTE]
>
> **Note**
>
> Please also refer to the [Dockerfile best
> practices](https://docs.docker.com/build/building/best-practices) for detailed
> information on Dockerfile best practices.

### 1. Minimize Layers

Each `RUN`, `COPY`, or `ADD` instruction creates a new layer. Minimize the number of layers by combining commands.

**Bad Example:**

```dockerfile theme={null}
RUN apt-get update
RUN apt-get install -y curl
```

**Good Example:**

```dockerfile theme={null}
RUN apt-get update && apt-get install -y curl
```

### 2. Leverage Layer Caching

Order instructions from least to most frequently changing to maximize layer caching.

**Example:**

```dockerfile theme={null}
# Install dependencies (changes less frequently)
COPY requirements.txt /app/
RUN pip install -r requirements.txt

# Copy application code (changes more frequently)
COPY . /app
```

### 3. Use `--mount=type=cache`

Utilize BuildKit's `--mount=type=cache` to cache directories across builds.

**Example:**

```dockerfile 1.3-labs theme={null}
# syntax=docker/dockerfile:1.3-labs
FROM python:3.9

# Use BuildKit cache for pip
RUN --mount=type=cache,target=/root/.cache/pip \
    pip install --upgrade pip

COPY requirements.txt /app/
RUN --mount=type=cache,target=/root/.cache/pip \
    pip install -r requirements.txt

COPY . /app
```

### 4. Multi-Stage Builds

Use multi-stage builds to reduce the final image size by copying only the necessary artifacts from intermediate stages.

**Example:**

```dockerfile 1.3 theme={null}
# syntax=docker/dockerfile:1.3
FROM python:3.9 AS builder
WORKDIR /app
COPY . .
RUN pip install --upgrade pip \
 && pip install -r requirements.txt

FROM python:3.9-slim
COPY --from=builder /app /app
WORKDIR /app
ENTRYPOINT ["python", "app.py"]
```

### 5. Clean Up After Installations

Remove unnecessary files and caches after installing packages to keep the image size small.

**Example:**

```dockerfile theme={null}
RUN apt-get update && apt-get install -y \
    build-essential \
 && rm -rf /var/lib/apt/lists/*
```

### 6. Use `.dockerignore`

Specify files and directories to ignore during the build process to avoid unnecessary files in the build context.

fal supports `.dockerignore` files when using `COPY` and `ADD` commands in your Dockerfile. Create a `.dockerignore` file in your project root to exclude files:

**Example:**

```plaintext theme={null}
# .dockerignore
.git
.venv
venv
__pycache__
*.pyc
*.pyo
.env
node_modules
```

You can also define ignore patterns programmatically:

```python theme={null}
from fal.container import ContainerImage

image = ContainerImage.from_dockerfile_str(dockerfile_str)
image.add_dockerignore(patterns=[".git", "*.pyc", ".env"])
```

If no `.dockerignore` exists, fal uses sensible defaults that exclude common development artifacts. See the [custom container image guide](https://fal.ai/docs/serverless/development/use-custom-container-image.md#dockerignore-support) for more details.

## Complete Example

Here is an example of a cache-efficient Dockerfile using the principles outlined above:

```dockerfile 1.3 theme={null}
# syntax=docker/dockerfile:1.3
FROM python:3.9 AS base
WORKDIR /app

# Install dependencies
COPY requirements.txt ./
RUN --mount=type=cache,target=/root/.cache/pip \
    pip install --upgrade pip \
 && pip install -r requirements.txt

# Copy source files
COPY . .

# Build the application
RUN python setup.py build

# Production image
FROM python:3.9-slim
COPY --from=base /app /app
WORKDIR /app
ENTRYPOINT ["python", "app.py"]
```

## Conclusion

By following these guidelines, you can create Dockerfiles that build efficiently and take full advantage of Docker Buildx and BuildKit's caching capabilities. This will lead to faster build times and reduced resource usage.

For more detailed information, refer to the [Docker documentation](https://docs.docker.com/build).
