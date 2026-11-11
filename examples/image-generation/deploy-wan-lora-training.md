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

# Deploy WAN LoRA Training

> Fine-tune WAN (text-to-video) with LoRA using your own short clips and images.

## 🚀 Try this Example

View the complete source code on [GitHub](https://github.com/fal-ai-community/fal-demos/blob/main/fal_demos/video/wan_trainer.py).

**Steps to run:**

1. Install fal:

```bash theme={null}
pip install fal
```

2. Authenticate (if not already done):

```bash theme={null}
fal auth login
```

3. Clone the demos repo and install dependencies:

```bash theme={null}
git clone https://github.com/fal-ai-community/fal-demos.git
cd fal-demos
pip install -e .
```

4. Run the trainer:

```bash theme={null}
fal run fal_demos/video/wan_trainer.py::WanLoRATrainerDemo
```

> [!TIP]
>
> This demo uses a GPU-heavy training stack and can run for a while depending on your step count.

## Deploy to fal

To host the trainer as an API:

```bash theme={null}
fal deploy fal_demos/video/wan_trainer.py::WanLoRATrainerDemo
```

## How it works

* Downloads WAN weights from Hugging Face and a pinned training repo.
* Prepares a dataset from your ZIP file (images and/or short videos).
* Runs LoRA training with DeepSpeed and returns the adapter weights.

## Input format

Your `training_data_url` should point to a ZIP containing:

* Images or videos (`.png`, `.jpg`, `.jpeg`, `.gif`, `.mp4`)
* Optional caption files (`my_clip.txt` next to `my_clip.mp4`)

If you set `auto_scale_input=true`, videos are fit to **81 frames at 16 fps** for WAN training.

## Example request

```python theme={null}
import fal_client

result = fal_client.submit(
    "your-username/wan-lora-trainer-demo",
    arguments={
        "training_data_url": "https://your-bucket/train.zip",
        "rank": 16,
        "number_of_steps": 400,
        "learning_rate": 2e-4,
        "trigger_phrase": "your-style",
        "auto_scale_input": True,
        "video_clip_mode": "single_beginning",
    },
).get()

print(result["lora_file"]["url"])
```
