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

# Deploy a ComfyUI Workflow as an API on fal

> Turn a ComfyUI workflow into an autoscaling API on fal Serverless: export the API JSON, package custom nodes and models, deploy, call it, and see how billing works.

This guide turns a ComfyUI workflow that works on your machine into a private, autoscaling HTTP API on [fal Serverless](../../documentation/serverless.md). The app runs the ComfyUI server inside a [custom container](../../documentation/development/use-custom-container-image.md), queues your workflow on it for each request, and returns the output images through fal's CDN.

You will:

1. Export the workflow in ComfyUI's API format.
2. Package ComfyUI, your custom nodes, and your model weights.
3. Write the `fal.App` that runs the workflow.
4. Test it with `fal run`, then deploy it with `fal deploy`.
5. Set the autoscaling parameters.
6. Call the endpoint with cURL and Python.

The [ComfyUI SDXL Turbo example](deploy-comfyui-server.md) is a complete, single-model version of this pattern. This guide covers the parts that change when you bring your own workflow: custom nodes, several models, and scaling.

## Prerequisites

* A fal account with [Serverless access](../../documentation/serverless.md), and the fal CLI installed and logged in:

```bash theme={null}
pip install fal
fal auth login
```

* A ComfyUI workflow that runs correctly in your local ComfyUI.
* The download URL of every model file the workflow loads (checkpoints, LoRAs, VAEs, ControlNets, and so on).

## Step 1: Export the Workflow API JSON

ComfyUI saves workflows in two formats. The normal save format describes the canvas layout. The **API format** describes only the nodes and their inputs, keyed by node ID. The ComfyUI server's `/prompt` endpoint accepts only the API format.

1. Open the workflow in ComfyUI and run it once to confirm it works.
2. Export it in API format. In the current ComfyUI interface this is **File > Export (API)** in the top menu. In the legacy menu, first turn on **Enable dev mode options (API save, etc.)** in the settings, then click **Save (API Format)**.
3. Save the file as `workflow_api.json` next to your app file.

The file maps each node ID to its `class_type` and `inputs`:

```json theme={null}
{
  "6": {
    "inputs": {"text": "a photo of a cat", "clip": ["4", 1]},
    "class_type": "CLIPTextEncode"
  },
  "3": {
    "inputs": {"seed": 42, "steps": 20, "model": ["4", 0], "positive": ["6", 0], "negative": ["7", 0], "latent_image": ["5", 0]},
    "class_type": "KSampler"
  }
}
```

Write down the node IDs of the inputs you want to expose in your API, such as the positive prompt text and the sampler seed. You set them in Step 3.

To see every node type the workflow uses, run:

```bash theme={null}
python -c "import json; print(sorted({n['class_type'] for n in json.load(open('workflow_api.json')).values()}))"
```

Any node type that is not part of core ComfyUI comes from a custom node package. You install those packages in Step 2.

## Step 2: Package Custom Nodes and Models

Your project needs two files:

```
my-comfy-app/
├── comfy_app.py         # the fal.App (Step 3)
└── workflow_api.json    # exported in Step 1
```

### ComfyUI and custom nodes go in the container image

Install ComfyUI and each custom node package in the Dockerfile. Pin each repository to a commit or tag, so that a rebuild cannot pick up an incompatible change:

```python theme={null}
DOCKERFILE_STR = f"""
FROM python:3.11-slim

RUN apt-get update && apt-get install -y --no-install-recommends \\
    git curl wget libgl1 libglib2.0-0 && rm -rf /var/lib/apt/lists/*

# The PyTorch CUDA wheels include the CUDA libraries
RUN pip install --no-cache-dir torch==2.11.0 torchvision torchaudio \\
    --extra-index-url https://download.pytorch.org/whl/cu128

# ComfyUI, pinned
RUN git clone https://github.com/comfyanonymous/ComfyUI /app/ComfyUI \\
 && cd /app/ComfyUI && git checkout <comfyui-tag-or-commit> \\
 && pip install --no-cache-dir -r requirements.txt

# One block per custom node package, pinned
RUN git clone https://github.com/<owner>/<custom-node-repo> /app/ComfyUI/custom_nodes/<custom-node-repo> \\
 && cd /app/ComfyUI/custom_nodes/<custom-node-repo> && git checkout <commit-sha> \\
 && if [ -f requirements.txt ]; then pip install --no-cache-dir -r requirements.txt; fi

# The workflow file, uploaded to the fal CDN at deploy time
ADD {WORKFLOW_URL} /app/workflow_api.json

# Install these last so fal's runtime gets compatible versions
RUN pip install --no-cache-dir boto3==1.35.74 protobuf==4.25.1 pydantic==2.10.6
"""
```

The fal runtime has two requirements for the image:

* The image must include `curl`.
* The `pydantic`, `protobuf`, and `boto3` packages must be compatible with the fal SDK. Install them last, as above, to meet this requirement. See [fal platform requirements](../../documentation/development/use-custom-container-image.md#fal-platform-requirements).

### Model weights go in persistent storage

Do not bake large model files into the image. A larger image takes longer to pull on every cold start. Download the weights in `setup()` with [`download_model_weights`](../../documentation/development/download-model-weights-and-files.md). It saves each file under `/data`, the persistent storage that all your runners share, and skips the download when the file is already there. Only the first runner pays the download time.

List each model by its path under ComfyUI's `models/` directory, so the workflow's loader nodes find it under the same file name:

```python theme={null}
MODELS = {
    "checkpoints/sd_xl_base_1.0.safetensors":
        "https://huggingface.co/stabilityai/stable-diffusion-xl-base-1.0/resolve/main/sd_xl_base_1.0.safetensors",
    "loras/my_style_lora.safetensors":
        "https://huggingface.co/<owner>/<repo>/resolve/main/my_style_lora.safetensors",
}
```

For gated or private Hugging Face repositories, store your token as a [secret](../../documentation/development/manage-secrets-securely.md). Secrets reach your runners as environment variables:

```bash theme={null}
fal secrets set HF_TOKEN=hf_...
```

## Step 3: Write the App

Save this as `comfy_app.py`. Replace the node IDs in `_build_workflow()` with the IDs you wrote down in Step 1.

```python theme={null}
import json
import os
import random
import subprocess
import time
from copy import deepcopy
from pathlib import Path
from urllib.parse import urlparse

import fal
import requests
from fal.container import ContainerImage
from fal.toolkit import File, Image, download_model_weights
from fastapi import Request
from pydantic import BaseModel, Field

# Upload the workflow to the fal CDN so the Dockerfile can ADD it.
WORKFLOW_PATH = Path(__file__).parent / "workflow_api.json"
WORKFLOW_URL = File.from_path(str(WORKFLOW_PATH), repository="cdn").url

DOCKERFILE_STR = f"""..."""  # the Dockerfile from Step 2

MODELS = {...}  # the model list from Step 2


class Input(BaseModel):
    prompt: str = Field(description="Positive prompt.", examples=["a photo of a cat"])
    seed: int = Field(default_factory=lambda: random.randint(0, 2**31 - 1))


class Output(BaseModel):
    images: list[Image]
    seed: int


class ComfyWorkflow(fal.App):
    machine_type = "GPU-A100"
    image = ContainerImage.from_dockerfile_str(DOCKERFILE_STR)

    # Autoscaling (see Step 5)
    keep_alive = 300
    min_concurrency = 0
    max_concurrency = 5

    COMFY_URL = "http://127.0.0.1:8188"
    COMFY_DIR = Path("/app/ComfyUI")

    def setup(self):
        hf_token = os.getenv("HF_TOKEN")

        # Download weights to /data and link them where ComfyUI looks for them.
        for model_path, url in MODELS.items():
            headers = None
            # Send the Hugging Face token only to Hugging Face.
            if hf_token and urlparse(url).hostname == "huggingface.co":
                headers = {"Authorization": f"Bearer {hf_token}"}
            downloaded = download_model_weights(url, request_headers=headers)
            target = self.COMFY_DIR / "models" / model_path
            target.parent.mkdir(parents=True, exist_ok=True)
            if target.exists() or target.is_symlink():
                target.unlink()
            target.symlink_to(downloaded)

        # Start the ComfyUI server in the background and wait until it answers.
        self.process = subprocess.Popen(
            ["python", "main.py", "--listen", "127.0.0.1", "--port", "8188"],
            cwd=str(self.COMFY_DIR),
        )
        for _ in range(300):
            if self.process.poll() is not None:
                raise RuntimeError("ComfyUI server exited during startup")
            try:
                if requests.get(f"{self.COMFY_URL}/system_stats", timeout=5).ok:
                    break
            except requests.ConnectionError:
                pass
            time.sleep(1)
        else:
            raise TimeoutError("ComfyUI server did not start")

        with open("/app/workflow_api.json", encoding="utf-8") as f:
            self.workflow = json.load(f)

    def _build_workflow(self, input: Input) -> dict:
        workflow = deepcopy(self.workflow)
        workflow["6"]["inputs"]["text"] = input.prompt  # CLIPTextEncode (positive)
        workflow["3"]["inputs"]["seed"] = input.seed    # KSampler
        return workflow

    @fal.endpoint("/generate")
    def generate(self, input: Input, request: Request) -> Output:
        resp = requests.post(
            f"{self.COMFY_URL}/prompt",
            json={"prompt": self._build_workflow(input)},
            timeout=30,
        )
        resp.raise_for_status()
        prompt_id = resp.json()["prompt_id"]

        deadline = time.time() + 600
        while True:
            resp = requests.get(f"{self.COMFY_URL}/history/{prompt_id}", timeout=30)
            resp.raise_for_status()
            entry = resp.json().get(prompt_id)
            status = (entry or {}).get("status", {})
            if status.get("status_str") == "error":
                raise RuntimeError(f"The workflow failed: {status.get('messages')}")
            if status.get("completed"):
                break
            if time.time() > deadline:
                # Stop the prompt so it does not delay the next request.
                requests.post(f"{self.COMFY_URL}/queue", json={"delete": [prompt_id]}, timeout=30)
                requests.post(f"{self.COMFY_URL}/interrupt", timeout=30)
                raise TimeoutError("The workflow did not complete")
            time.sleep(0.5)

        images = []
        for node_output in entry["outputs"].values():
            for item in node_output.get("images", []):
                if item.get("type") != "output":
                    continue  # skip PreviewImage files in temp/
                path = self.COMFY_DIR / "output" / item.get("subfolder", "") / item["filename"]
                images.append(Image.from_path(str(path), request=request))
        if not images:
            raise RuntimeError("The workflow produced no images")
        return Output(images=images, seed=input.seed)
```

How it works:

* **`setup()` runs once per runner.** It downloads or reuses the weights, starts ComfyUI, and loads the workflow template. Requests reuse the running ComfyUI server, so models stay in GPU memory between requests.
* **Each request copies the template** and sets only the inputs your API exposes. Every other node keeps the value from your export.
* **ComfyUI's HTTP API** does the work: `POST /prompt` queues the workflow, and `GET /history/{prompt_id}` reports its outputs when it completes.
* **`Image.from_path`** uploads each output file to the fal CDN and returns its URL. Passing `request` attaches the metadata the [playground](../../documentation/model-apis/playground.md#your-own-apps) uses.

Choose `machine_type` by the VRAM your workflow needs. See [machine types](../../documentation/deployment/machine-types.md). If the first model download takes longer than the default setup limit, raise [`startup_timeout`](../../documentation/deployment/scale-your-application.md).

The Dockerfile in Step 2 fetches the workflow file with `ADD` from its CDN URL. You can instead put `COPY workflow_api.json /app/workflow_api.json` in the Dockerfile. fal uploads the local files that `COPY` names to the build context when you deploy. This is an [experimental feature](../../documentation/development/use-custom-container-image.md#copy-and-build-context). Container apps do not support `app_files`.

## Step 4: Test, Then Deploy

Run the app on a temporary runner first. `fal run` builds the image, runs `setup()`, and gives you a temporary URL, so missing nodes and wrong model paths show up before you deploy:

```bash theme={null}
fal run comfy_app.py::ComfyWorkflow
```

When the test requests succeed, deploy it:

```bash theme={null}
fal deploy comfy_app.py::ComfyWorkflow --app-name comfy-workflow
```

The app is now available at the endpoint ID `your-username/comfy-workflow`. Apps are private by default, so callers need a fal API key. Each deploy creates a new revision that you can [roll back](../../documentation/deployment/rollbacks.md) to. See [Deploy to Production](../../documentation/deployment/deploy-to-production.md) for authentication modes and environments.

## Step 5: Set Autoscaling

fal starts runners when requests arrive and stops them when they are idle. These [scaling parameters](../../documentation/deployment/scale-your-application.md) control the trade-off between latency and cost:

| Parameter | Default | What it does for a ComfyUI app |
| - | - | - |
| `keep_alive` | 60 seconds | How long an idle runner stays warm. A warm runner has ComfyUI running and the models loaded, so the next request skips the cold start. |
| `min_concurrency` | 0 | Runners that stay up even with no traffic. `0` scales to zero. `1` or more removes cold starts, but those runners are billed all the time. |
| `max_concurrency` | 2 | The maximum number of runners. Raise it for more parallel requests, or use it to cap spend. |
| `concurrency_buffer` | 0 | Extra warm runners above current load, for traffic spikes. |
| `max_multiplexing` | 1 | Requests per runner at the same time. ComfyUI executes one prompt at a time, so keep the default of 1. |

Set them as class attributes, as in Step 3, or change them on a live app without a redeploy:

```bash theme={null}
fal apps scale comfy-workflow --keep-alive 300 --min-concurrency 0 --max-concurrency 5
```

> [!NOTE]
>
> A redeploy keeps the scale settings of the previous deployment. To apply the values in your code instead, deploy with `--reset-scale`:
>
> ```bash theme={null}
> fal deploy comfy_app.py::ComfyWorkflow --app-name comfy-workflow --reset-scale
> ```

## Step 6: Call the Endpoint

The workflow runs at the `/generate` path, so append `/generate` to the endpoint ID.

> [tab] cURL
>
> Submit to the queue. This is the right default for workflows that take more than a few seconds:
>
> ```bash theme={null}
> curl -X POST https://queue.fal.run/your-username/comfy-workflow/generate \
>   -H "Authorization: Key $FAL_KEY" \
>   -H "Content-Type: application/json" \
>   -d '{"prompt": "a photo of a cat"}'
> ```
>
> The response contains a `request_id`, a `status_url`, and a `response_url`. Poll the `status_url` until the request completes, then fetch the result from the `response_url`, both with the same `Authorization` header. See [Queue](../../documentation/model-apis/inference/queue.md).
>
> For a short workflow, you can call the endpoint synchronously and wait for the result:
>
> ```bash theme={null}
> curl -X POST https://fal.run/your-username/comfy-workflow/generate \
>   -H "Authorization: Key $FAL_KEY" \
>   -H "Content-Type: application/json" \
>   -d '{"prompt": "a photo of a cat"}'
> ```

> [tab] Python
>
> ```bash theme={null}
> pip install fal-client
> export FAL_KEY="your-api-key"
> ```
>
> ```python theme={null}
> import fal_client
>
> result = fal_client.subscribe(
>     "your-username/comfy-workflow/generate",
>     arguments={"prompt": "a photo of a cat", "seed": 42},
> )
> print(result["images"][0]["url"])
> ```
>
> `subscribe` submits the request to the queue and waits for the result. See [Calling Your Endpoints](../../documentation/development/calling-your-endpoints.md) for async calls, webhooks, and the JavaScript client.

## What Billing Looks Like

Serverless apps are billed for the time their runners are alive, per second, at the rate of the machine type. A ComfyUI workflow is not billed per image. See [Serverless pricing](../../documentation/serverless/pricing.md) for the full rules and [fal.ai/pricing](https://fal.ai/pricing) for the per-second rate of each machine type.

| Runner state | Billed | In a ComfyUI app |
| - | - | - |
| Waiting for hardware, pulling the image | No | |
| `setup()` | Yes | Model downloads (first runner only) and ComfyUI startup |
| Running a request | Yes | The workflow execution |
| Idle, including `keep_alive` | Yes | The warm runner waiting for the next request |

Requests that fail with a server error (HTTP 500 or higher) are not charged. Runners with more than one GPU are billed for each GPU.

To control cost:

* Keep `min_concurrency` at 0 unless you need no cold starts. Each runner it keeps up is billed even without traffic.
* Size `keep_alive` to the gap between your requests. A long `keep_alive` on sparse traffic is billed idle time.
* Keep weights in `/data` (Step 2), so new runners do not download them again.

The [App Analytics](../../documentation/serverless/observability/app-analytics.md) page shows the cost, request count, and runner use of each app. For more ways to cut cost, see [Optimizing Costs](../../documentation/serverless/optimizations/optimizing-costs.md).

## Troubleshooting

* **"Model not found" or a missing file in a loader node.** The key in `MODELS` must match the file name in the workflow JSON, under the right `models/` subfolder.
* **A node type is missing when the workflow runs.** Install its custom node package in the Dockerfile (Step 2), then run `fal run` again.
* **The runner times out during setup.** Raise `startup_timeout`. The first runner downloads all the weights.

## Next Steps

* [Deploy a ComfyUI SDXL Turbo App](deploy-comfyui-server.md): the complete single-model example, with an architecture diagram.
* [Use a Custom Container Image](../../documentation/development/use-custom-container-image.md): Dockerfile options and private registries.
* [Scale Your Application](../../documentation/deployment/scale-your-application.md): all scaling parameters.
* [Optimize Cold Starts](../../documentation/serverless/optimizations/optimize-cold-starts.md): reduce the time before the first request runs.
