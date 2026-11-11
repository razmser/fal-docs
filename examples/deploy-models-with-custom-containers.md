> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../documentation/quickstart.md) and the [Model APIs overview](../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Deploy Models with Custom Containers

> fal now supports running apps within custom Docker containers, providing greater flexibility and control over your environment.

### Example: Using Custom Containers with fal Apps

Here's a complete example demonstrating how to use custom containers with `fal`.

```python theme={null}
import fal

from fal.container import ContainerImage

dockerfile_str = """
FROM python:3.11

RUN apt-get update && apt-get install -y ffmpeg
RUN pip install pyjokes ffmpeg-python
"""

custom_image = ContainerImage.from_dockerfile_str(
    dockerfile_str,
    registries={
        "https://my.registry.io/": {
            "username": <username>,
            "password": <password>,
        },
    },
)

class Test(fal.App):
    image = custom_image
    machine_type = "GPU"

    requirements = ["torch"]

    def setup(self):
        import subprocess
        subprocess.run(["nvidia-smi"])

    @fal.endpoint("/")
    def index(self):
        return "Hello, World!"
```

#### Detailed Explanation

1. **Importing fal and ContainerImage**:

```python theme={null}
import fal
from fal.container import ContainerImage
```

2. **Creating a Dockerfile String**:
   A multi-line string (`dockerfile_str`) is defined, specifying the base image as `python:3.11`, and installing `ffmpeg` and `pyjokes` packages.

```python theme={null}
dockerfile_str = """
FROM python:3.11

RUN apt-get update && apt-get install -y ffmpeg
RUN pip install pyjokes ffmpeg-python
"""
```

> [!NOTE]
>
> **Version mismatch**
>
> Ensure that the Python version in the Dockerfile matches the Python version in
> your local environment that you use to run the app.
>
> This is required to avoid any compatibility issues. We use `pickle` to serialize
> the app under the hood, and the Python versions must match to avoid any
> serialization issues.
>
> That being said, we are constantly working on improving this experience.

Alternatively, you can use a Dockerfile path to specify the Dockerfile location:

```python theme={null}
import pathlib
PWD = Path(__file__).resolve().parent

class Test(fal.App):
    image = ContainerImage.from_dockerfile(f"{PWD}/Dockerfile")
```

#### Running the App

To run the app, save the code to a file (e.g., `test_container.py`) and execute it using the `fal run` command:

```bash theme={null}
fal run test_container.py
```

This example demonstrates how to leverage Docker containers in fal, enabling customized execution environments for your apps. For more details and advanced usage, refer to the [fal Container Documentation](https://fal.ai/docs/serverless/development/use-custom-container-image.md).
