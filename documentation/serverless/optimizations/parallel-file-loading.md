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

# Parallel File Loading

> Speed up model loading by pre-reading files in parallel from /data.

The `/data` filesystem on fal is a distributed, parallel storage system. It performs best when multiple files are read concurrently. Sequential file reads -- loading one weight file at a time -- significantly underutilize the filesystem and result in slower cold starts.

## The Problem

Most model loading libraries (Hugging Face, PyTorch) read weight files **sequentially** -- one shard at a time. On a distributed filesystem, this leaves most of the available bandwidth idle:

```
Sequential:
  time ──────────────────────────────────►
  ┌──file1──┐┌──file2──┐┌──file3──┐┌──file4──┐

Parallel:
  time ──────────────────►
  ┌──file1──┐
  ┌──file2──┐
  ┌──file3──┐
  ┌──file4──┐
```

## The Solution: Pre-Read Files in Parallel

Before loading your model, pre-read all weight files into the OS page cache using parallel I/O. When the model loader then reads the files, they're already cached in memory and load instantly.

```python theme={null}
import subprocess

MODEL_DIR = "/data/models/my-model"

# call, not check_call: one unreadable file should not fail setup
subprocess.call(
    f"find -L '{MODEL_DIR}' -type f -print0 | xargs -0 -r -P 32 -n 1 cat > /dev/null",
    shell=True
)
```

This reads up to 32 files simultaneously, pulling them into the page cache. The subsequent `from_pretrained()` call then reads from cache instead of the network.

`-L` follows symlinks. `-print0` and `-0` keep file names with spaces or quotes intact.

## Using It in Your App

Add the pre-read step at the beginning of your `setup()` method, before model loading:

```python theme={null}
import fal
import subprocess

class MyModel(fal.App):
    machine_type = "GPU-H100"

    def setup(self):
        import torch
        from diffusers import StableDiffusionXLPipeline

        model_dir = "/data/models/stable-diffusion-xl"

        # Pre-read all model files in parallel
        subprocess.call(
            f"find -L '{model_dir}' -type f -print0 | xargs -0 -r -P 32 -n 1 cat > /dev/null",
            shell=True
        )

        # Now load the model -- files are already in page cache
        self.pipe = StableDiffusionXLPipeline.from_pretrained(
            model_dir,
            torch_dtype=torch.float16,
        ).to("cuda")
```

### Python Helper

For a cleaner approach, wrap it in a function:

```python theme={null}
import subprocess
from pathlib import Path

def preload_files(directory: str, parallelism: int = 32):
    """Pre-read all files in a directory into the OS page cache."""
    subprocess.call(
        f"find -L '{directory}' -type f -print0 | xargs -0 -r -P {parallelism} -n 1 cat > /dev/null",
        shell=True
    )
```

```python theme={null}
def setup(self):
    preload_files("/data/models/my-model")
    self.model = load_model("/data/models/my-model")
```

### Hugging Face Cache

In a Hugging Face cache, the files in `snapshots/<commit>/` are symlinks into `blobs/`. Without `-L`, `find -type f` matches none of them. The command reads nothing and still exits 0.

Pre-read the snapshot folder, not the repo folder (`models--org--name`). The repo folder also holds old revisions, so you read more data than the model uses. `snapshot_download(..., local_files_only=True)` returns the snapshot folder without downloading anything:

```python theme={null}
from huggingface_hub import snapshot_download

try:
    # Use the same revision, cache_dir and allow_patterns as your load call
    model_dir = snapshot_download("org/name", local_files_only=True)
    preload_files(model_dir)  # model_dir is .../models--org--name/snapshots/<commit>
except Exception as e:  # not in the cache yet, for example on the first runner
    print(f"preload skipped: {e}")
```

## When to Use This

| Scenario | Recommendation |
| - | - |
| Loading Hugging Face models from `/data` | Use parallel pre-reading |
| Loading custom PyTorch checkpoints with multiple shard files | Use parallel pre-reading |
| Loading a single large file | Less benefit -- the bottleneck is single-file transfer speed. Consider [FlashPack](flashpack.md) instead |
| Models already in local node cache (warm runners) | Minimal benefit -- files are already fast to read |

## How It Works

fal's `/data` is a distributed filesystem that can serve many files concurrently at high throughput. When you read files sequentially, you use only a fraction of the available bandwidth. The `xargs -P 32` approach fires off 32 concurrent `cat` commands, each reading a different file. The OS caches the file contents in memory (page cache), so when your model loader reads the same files moments later, it reads from RAM instead of the network.

> [!TIP]
>
> Adjust the parallelism (`-P 32`) based on your model. For models with many small files (e.g., 100+ safetensors shards), higher parallelism helps. For models with a few large files, lower parallelism (8-16) is sufficient.

## Comparison with FlashPack

| | Parallel Pre-Reading | FlashPack |
| - | - | - |
| **What it does** | Pre-reads files into OS cache | Streams tensors directly to GPU |
| **Works with** | Any files in any format | PyTorch models (custom format) |
| **Requires conversion** | No -- works with existing files | Yes -- must convert to `.flashpack` format |
| **Best for** | Quick win with existing models | Maximum performance with converted models |

Both techniques can be combined -- use parallel pre-reading as a quick improvement, then migrate to FlashPack for even faster loading.

> [card] [FlashPack](flashpack.md)
>
> High-throughput tensor loading at up to 25 Gbps

> [card] [Persistent Storage](../../development/use-persistent-storage.md)
>
> How /data works and caching behavior
