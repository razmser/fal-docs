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

# Generate video with WAN 2.2 across four GPUs

> Serve a text-to-video model too big for one GPU using fal.distributed and Ulysses sequence parallelism.

> [card] [View on GitHub](https://github.com/fal-ai-community/fal-serverless-examples/tree/main/04-genmedia-patterns/multi-gpu-video)
>
> Full source for this example in fal-serverless-examples.

Large video models may not fit on a single GPU. WAN 2.2 A14B has two
14-billion-parameter expert transformers, and the weights alone are about 70 GB in
bf16. This example serves the model across four H100s with `fal.distributed`. The
`DistributedRunner` starts one worker for each GPU, and diffusers' context parallelism
divides every denoising step across the four workers. One prompt, one video, four GPUs
cooperating.

<video autoPlay loop muted playsInline controls>
  <source src="https://v3b.fal.media/files/b/0aa63c50/SBFn10wuHkWfhjyEX3_k__f4c83d934a01479eae461e06a6c4d35b-wan22-multi-gpu-diffuse-hero.mp4" type="video/mp4" />
</video>

The clip emerges the way the sampler saw it: denoising, visualized over the real
output of the example's validation run (720p, 81 frames, 27 steps, seed 7 -
generated in 282 s on 4× GPU-H100).

## Running the example

1. Install fal:

```bash theme={null}
pip install fal
```

2. Authenticate (if not already done):

```bash theme={null}
fal auth login
```

3. Clone the examples repository and enter this example's directory:

```bash theme={null}
git clone https://github.com/fal-ai-community/fal-serverless-examples.git
cd fal-serverless-examples/04-genmedia-patterns/multi-gpu-video
```

4. Run the app:

```bash theme={null}
fal run app.py
```

This starts an ephemeral app on four real GPUs and prints its playground and API URLs.
`fal run` keeps the app alive until you press Ctrl-C.

> [!NOTE]
>
> The first-ever run downloads \~126 GB of fp32 weights and stores a bf16 copy on your
> account's [`/data` volume](../../documentation/development/use-persistent-storage.md) (about
> 35 minutes, once). Every run after that boots from the converted copy in about
> 8 minutes.

## Splitting one generation across four GPUs

Data parallelism gives each GPU a full, separate generation: four GPUs make four
different videos, and latency does not change. This example uses the opposite
strategy. Ulysses sequence parallelism divides the token sequence of **one**
generation across all four workers. This is the right trade-off when the model itself
is the bottleneck. At every attention layer, an all-to-all exchange trades tokens for
heads: each GPU sees the full sequence for 10 of the 40 attention heads, then the
shards return to their own GPUs.

![Ulysses sequence parallelism in three phases: the 81-frame sequence is sharded across four GPUs, an all-to-all trades frames for heads at every attention layer so each GPU holds all 81 frames for 10 of the 40 heads, then shards return home](https://mintcdn.com/fal-d8505a2e/k-eFpTMgC06rFqwz/images/examples/multi-gpu-ulysses-light.svg?fit=max\&auto=format\&n=k-eFpTMgC06rFqwz\&q=85\&s=00992cc724ef22d13d5a1f63889be28b)

![Ulysses sequence parallelism in three phases: the 81-frame sequence is sharded across four GPUs, an all-to-all trades frames for heads at every attention layer so each GPU holds all 81 frames for 10 of the 40 heads, then shards return home](https://mintcdn.com/fal-d8505a2e/k-eFpTMgC06rFqwz/images/examples/multi-gpu-ulysses-dark.svg?fit=max\&auto=format\&n=k-eFpTMgC06rFqwz\&q=85\&s=fb7381f9a0db53758eb652b66115f12e)

See [Multi-GPU Workloads](../../documentation/serverless/distributed/overview.md) for the full
strategy taxonomy. The next nine sections walk through the app one file top to
bottom: `app.py`.

## Importing fal.distributed and pinning the model

`fal.distributed` provides the two classes this example teaches:
[`DistributedRunner`](../../documentation/serverless/distributed/api-reference.md#distributedrunner)
and
[`DistributedWorker`](../../documentation/serverless/distributed/api-reference.md#distributedworker).
`MODEL_ID` pins
[`Wan-AI/Wan2.2-T2V-A14B-Diffusers`](https://huggingface.co/Wan-AI/Wan2.2-T2V-A14B-Diffusers)
at an exact revision; the model is Apache-2.0 and ungated, so no Hugging Face token
appears anywhere. `CONVERTED_DIR` names the one-time bf16 copy on `/data`. Sequence
parallelism replicates the weights on every rank, so the half-size copy halves every
worker's load time on every boot.

```python app.py theme={null}
import os
import random
import shutil
import time
import uuid
from pathlib import Path
from typing import Any, Literal

import fal
from fal.distributed import DistributedRunner, DistributedWorker
from fal.toolkit import File
from fastapi import HTTPException
from pydantic import BaseModel, Field, field_validator

MODEL_ID = "Wan-AI/Wan2.2-T2V-A14B-Diffusers"
MODEL_REVISION = "5be7df9619b54f4e2667b2755bc6a756675b5cd7"
NUM_GPUS = 4

# HF repo is fp32 (~126 GB); convert once to bf16 on /data (persists account-wide).
CONVERTED_DIR = "/data/models/wan22-t2v-a14b-bf16"

# 16 fps; sizes are (height, width).
RESOLUTIONS: dict[str, tuple[int, int]] = {
    "480p": (480, 832),
    "720p": (720, 1280),
}
```

## Defining the request schema

Every `Field` carries a description, and the fields with `examples` power the
generated [playground](../../documentation/model-apis/playground.md) form and deterministic
smoke requests. The validator enforces WAN's frame rule: `num_frames` must satisfy
`(n - 1) % 4 == 0`. The two guidance scales exist because the A14B is a
mixture-of-experts pair - one expert denoises the high-noise steps, the other the
low-noise steps, and each takes its own classifier-free-guidance strength. `seed` is
optional: the server picks one when omitted, and the same seed with the same inputs
reproduces the clip.

```python app.py theme={null}
class Input(BaseModel):
    prompt: str = Field(
        description="What happens in the video. Long, specific prompts steer WAN best.",
        examples=["a red panda napping on a mossy branch, soft morning light"],
    )
    negative_prompt: str = Field(
        default="",
        description="What to avoid in the video.",
    )
    resolution: Literal["480p", "720p"] = Field(
        default="480p",
        description=(
            "480p (832x480) for fast iteration; 720p (1280x720) for final quality."
        ),
        examples=["480p"],
    )
    num_frames: int = Field(
        default=33,
        ge=9,
        le=81,
        description=(
            "Clip length in frames at 16 fps; must satisfy (n - 1) % 4 == 0. "
            "81 is ~5 s."
        ),
        examples=[33],
    )
    num_inference_steps: int = Field(
        default=6,
        ge=2,
        le=40,
        description=(
            "Denoising steps. 6 is a fast preview; WAN's native quality default is 27."
        ),
        examples=[6],
    )
    guidance_scale: float = Field(
        default=3.5,
        ge=1.0,
        le=10.0,
        description="Classifier-free guidance for the high-noise expert.",
    )
    guidance_scale_2: float = Field(
        default=4.0,
        ge=1.0,
        le=10.0,
        description="Classifier-free guidance for the low-noise expert.",
    )
    seed: int | None = Field(
        default=None,
        ge=0,
        le=2**32 - 1,
        description=(
            "Random when omitted. Echoed back; same seed + same inputs "
            "reproduces the clip."
        ),
    )

    @field_validator("num_frames")
    @classmethod
    def _frames_on_grid(cls, v: int) -> int:
        if (v - 1) % 4 != 0:
            raise ValueError(
                "num_frames must satisfy (num_frames - 1) % 4 == 0, "
                "e.g. 9, 17, 33, 49, 81"
            )
        return v
```

## Defining the response schema

The response carries the CDN-hosted clip, the seed that produced it, and per-phase
timings.

```python app.py theme={null}
class Output(BaseModel):
    # No sync_mode: multi-hundred-MB clips are unfit for data-URI transport.
    video: File = Field(description="The generated clip (mp4, 16 fps) on the fal CDN.")
    seed: int = Field(description="The seed that produced this video.")
    timings: dict[str, float] = Field(
        description="Wall-clock seconds per phase; 'generate' is the four-GPU denoise."
    )
```

On the wire, the validation run's response looked like this:

```json theme={null}
{
  "video": {
    "url": "https://v3b.fal.media/files/b/0aa5f482/O2ywRcibrXZ1YRCIu27CA_wan22-1d96a465e70c4e76ba364350cd034fc4.mp4",
    "content_type": "video/mp4",
    "file_name": "wan22-1d96a465e70c4e76ba364350cd034fc4.mp4",
    "file_size": 841093
  },
  "seed": 7,
  "timings": {"generate": 282.2}
}
```

## Loading the model and enabling parallel attention

One `WanWorker` process runs on each GPU. The runner initializes
`torch.distributed` (NCCL) **before** this method runs, so
`enable_parallelism` can attach to the ready process group. That single line per
transformer is the whole sequence-parallelism integration. Two techniques keep the
\~70 GB model inside an 80 GB H100: every rank loads the bf16 copy, and sequenced CPU
offload keeps only the active MoE expert resident (the two experts run in disjoint
denoising stages). Measured peak is 27-30 GB per GPU, with headroom to 720p × 81
frames.

```python app.py theme={null}
class WanWorker(DistributedWorker):
    """One process per GPU; torch.distributed is already initialized here."""

    def setup(self, **kwargs) -> None:
        import torch
        from diffusers import ContextParallelConfig, WanPipeline

        # Sequence, not weights, is sharded: every rank loads the full pipeline.
        pipe = WanPipeline.from_pretrained(CONVERTED_DIR, torch_dtype=torch.bfloat16)

        cp = ContextParallelConfig(ulysses_degree=self.world_size)
        pipe.transformer.enable_parallelism(config=cp)
        pipe.transformer_2.enable_parallelism(config=cp)

        pipe.vae.enable_tiling()
        # MoE stages are disjoint; offload keeps only the active expert (~30/80 GB).
        pipe.enable_model_cpu_offload(device=self.device)
        self.pipe = pipe
```

## Generating on all ranks, returning from rank 0

All four ranks execute the same call with the same seed. (Under data parallelism you
would do the opposite: per-rank seeds for different outputs.) Only rank 0 writes the
mp4 and returns its path; the other ranks return an empty dictionary - their
contribution happened inside the pipeline through NCCL collectives.

```python app.py theme={null}
    def __call__(  # type: ignore[override]  # intentional kwargs narrowing
        self,
        streaming: bool = False,  # force-injected by the runner - accept and ignore
        *,
        prompt: str,
        negative_prompt: str,
        height: int,
        width: int,
        num_frames: int,
        num_inference_steps: int,
        guidance_scale: float,
        guidance_scale_2: float,
        seed: int,
        **kwargs: Any,
    ) -> dict[str, Any]:
        import torch
        from diffusers.utils import export_to_video

        # Context parallelism needs identical inputs on every rank: all share one seed.
        generator = torch.Generator(device="cpu").manual_seed(seed)
        t0 = time.perf_counter()
        result = self.pipe(
            prompt=prompt,
            negative_prompt=negative_prompt,
            height=height,
            width=width,
            num_frames=num_frames,
            num_inference_steps=num_inference_steps,
            guidance_scale=guidance_scale,
            guidance_scale_2=guidance_scale_2,
            generator=generator,
            output_type="np",
        )
        generate_seconds = round(time.perf_counter() - t0, 1)

        if self.rank != 0:
            # Only rank 0 materializes the artifact and replies.
            return {}

        path = f"/tmp/wan22-{uuid.uuid4().hex}.mp4"
        export_to_video(result.frames[0], path, fps=16)
        return {"video_path": path, "timings": {"generate": generate_seconds}}
```

## Configuring the app for four GPUs

`num_gpus = 4` is a configurable parameter: fal allocates four H100s on one
machine, and they appear as `cuda:0..3` (see [machine
types](../../documentation/deployment/machine-types.md)). Multi-GPU billing is
`gpu_count × duration`, so `keep_alive=300` holds the warm window to 5 minutes.
`startup_timeout=3600` exists for one boot in the app's life: the first-ever download
plus the bf16 conversion. The `requirements` list is the authoritative set of
deploy-time dependencies; `pyzmq` is pinned explicitly because `fal.distributed` uses
it at runtime but the `fal` package does not depend on it.

```python app.py theme={null}
class MultiGpuVideo(fal.App, keep_alive=300):
    machine_type = "GPU-H100"
    num_gpus = NUM_GPUS  # class attribute, not a class kwarg
    app_auth = "private"
    # First boot/account: ~126 GB download + bf16 convert (~35 min); later boots ~8 min.
    startup_timeout = 3600
    # Authoritative; requirements.txt mirrors this exactly.
    requirements = [
        "fal==1.78.2",
        "torch==2.12.1",
        "diffusers==0.39.0",
        "transformers==5.3.0",
        "accelerate==1.14.0",
        "pyzmq==27.1.0",  # fal.distributed's transport; not a dependency of fal itself
        "hf-transfer==0.1.9",
        "ftfy==6.3.1",
        "imageio==2.37.4",
        "imageio-ffmpeg==0.6.0",
    ]
```

## Converting once, starting the runner, warming up

The app process converts the fp32 weights to bf16 one time per account (with an
atomic rename, so concurrent boots cannot corrupt the copy), then starts the runner.
The runner spawns the four workers, initializes NCCL, and waits for a readiness
signal from each. If a worker dies, `await runner.start()` fails with an error
instead of hanging. The warmup then runs one generation through the whole distributed
path at the default request shape, because attention kernels autotune for each shape
and a cold shape adds about 40 s to the first request. Worker errors come back as an `"error"` key, not an
exception, so the warmup asserts on it: a broken model never reports healthy.

![DistributedRunner lifecycle: the fal.App endpoint spawns four GPU workers that init NCCL before setup() and report READY; each request goes to rank 0, which broadcasts the same payload and seed to all ranks for collective compute, and only rank 0's result returns; a worker exception comes back as an error value the endpoint maps to a 5xx](https://mintcdn.com/fal-d8505a2e/k-eFpTMgC06rFqwz/images/examples/multi-gpu-worker-lifecycle-light.svg?fit=max\&auto=format\&n=k-eFpTMgC06rFqwz\&q=85\&s=8335cc5d8ea03e35c1d576d69df061ef)

![DistributedRunner lifecycle: the fal.App endpoint spawns four GPU workers that init NCCL before setup() and report READY; each request goes to rank 0, which broadcasts the same payload and seed to all ranks for collective compute, and only rank 0's result returns; a worker exception comes back as an error value the endpoint maps to a 5xx](https://mintcdn.com/fal-d8505a2e/k-eFpTMgC06rFqwz/images/examples/multi-gpu-worker-lifecycle-dark.svg?fit=max\&auto=format\&n=k-eFpTMgC06rFqwz\&q=85\&s=2473ad702bec3253a4788bfda87c9c77)

```python app.py theme={null}
    async def setup(self) -> None:
        os.environ["HF_HUB_ENABLE_HF_TRANSFER"] = "1"

        # CONVERTED_DIR appears only via atomic rename below: existence = complete copy.
        if not Path(CONVERTED_DIR).exists():
            import torch
            from diffusers import WanPipeline
            from huggingface_hub import snapshot_download

            snapshot_download(MODEL_ID, revision=MODEL_REVISION)
            pipe = WanPipeline.from_pretrained(
                MODEL_ID, revision=MODEL_REVISION, torch_dtype=torch.bfloat16
            )
            # /data is account-wide: concurrent first boots must stage in unique paths.
            tmp_dir = f"{CONVERTED_DIR}.tmp.{uuid.uuid4().hex}"
            pipe.save_pretrained(tmp_dir, safe_serialization=True)
            del pipe
            try:
                os.rename(tmp_dir, CONVERTED_DIR)
            except OSError:
                # Another boot won the race and already published its copy.
                shutil.rmtree(tmp_dir)

        self.runner = DistributedRunner(worker_cls=WanWorker, world_size=NUM_GPUS)
        await self.runner.start(timeout=1800)

        # Warm at the default request shape: attention kernels autotune per shape
        # (a cold shape adds ~40 s); worker errors arrive as an "error" key, not raised.
        warmup = await self.runner.invoke(
            _worker_payload(Input(prompt="warmup", num_inference_steps=2), seed=0)
        )
        assert "error" not in warmup, f"warmup failed: {warmup}"
        os.remove(warmup["video_path"])
```

## Serving requests

`@fal.endpoint("/")` serves `generate` at the app's root URL. It picks a seed when
the caller omitted one, sends the payload through the runner, and checks the
`"error"` contract: a worker failure becomes a 5xx response, which fal does not bill.
The queue does not retry a 500 automatically - only 503, 504, and connection failures
are retried (see [Retries](../../documentation/serverless/reliability/retries.md)) - so the
caller decides whether to resubmit. The mp4 uploads to fal's CDN, and the temp file is
removed either way - warm runners live on, and clips must not accumulate in `/tmp`.
The runner processes one request at a time (fal's default `max_multiplexing = 1`
admits a single request per runner), so this endpoint scales by adding runners, not
by sending more requests to one runner.

```python app.py theme={null}
    @fal.endpoint("/")
    async def generate(self, request: Input) -> Output:
        seed = (
            request.seed if request.seed is not None else random.randint(0, 2**32 - 1)
        )
        result = await self.runner.invoke(_worker_payload(request, seed))
        if "error" in result:
            # 5xx: not billed, and the platform retries on healthy hardware.
            raise HTTPException(
                status_code=500, detail=f"generation failed: {result['error']}"
            )

        try:
            video = File.from_path(result["video_path"], content_type="video/mp4")
        finally:
            # keep_alive runners live on: never leave multi-MB clips in /tmp.
            os.remove(result["video_path"])
        return Output(video=video, seed=seed, timings=result["timings"])
```

## Building the worker payload

The endpoint and the warmup build the worker payload the same way: resolve the
resolution to pixels and pass one explicit seed to every rank.

```python app.py theme={null}
def _worker_payload(request: Input, seed: int) -> dict[str, Any]:
    height, width = RESOLUTIONS[request.resolution]
    return {
        "prompt": request.prompt,
        "negative_prompt": request.negative_prompt,
        "height": height,
        "width": width,
        "num_frames": request.num_frames,
        "num_inference_steps": request.num_inference_steps,
        "guidance_scale": request.guidance_scale,
        "guidance_scale_2": request.guidance_scale_2,
        "seed": seed,
    }
```

## Deploying to production

From the example directory, deploy the app:

```bash theme={null}
fal deploy app.py
```

This registers `multi-gpu-video`. To test its endpoints in a browser, follow
[Testing Your Own Apps](../../documentation/model-apis/playground.md#your-own-apps).
Generations take minutes at production settings, so always call the
endpoint through the [queue API](../../documentation/model-apis/inference/queue.md), never
through raw synchronous HTTP.

The clients below authenticate with `FAL_KEY` (`fal auth login` covers the CLI only):
create a key at [fal.ai/dashboard/keys](https://fal.ai/dashboard/keys) and export it
as `FAL_KEY="your_key_id:your_key_secret"`.

**Python**

```bash theme={null}
pip install fal-client
```

```python theme={null}
import fal_client

result = fal_client.subscribe(
    "<your-username>/multi-gpu-video",
    arguments={
        "prompt": "a red panda napping on a mossy branch, soft morning light",
        "resolution": "720p",
        "num_frames": 81,
        "num_inference_steps": 27,
        "seed": 7,
    },
)
print(result["video"]["url"])
```

**JavaScript**

```bash theme={null}
npm install @fal-ai/client
```

```javascript theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("<your-username>/multi-gpu-video", {
  input: {
    prompt: "a red panda napping on a mossy branch, soft morning light",
    resolution: "720p",
    num_frames: 81,
    num_inference_steps: 27,
  },
});
console.log(result.data.video.url);
```

**curl (queue API)**

```bash theme={null}
curl -X POST "https://queue.fal.run/<your-username>/multi-gpu-video" \
  -H "Authorization: Key $FAL_KEY" -H "Content-Type: application/json" \
  -d '{"prompt": "a red panda napping on a mossy branch, soft morning light", "resolution": "720p", "num_frames": 81, "num_inference_steps": 27}'
# Returns {"request_id": "...", "status_url": "...", ...}: poll the status_url, then fetch the result
```

That 720p call is also this example's validation protocol: deploy, run it once,
confirm the returned mp4 plays. (Last run 2026-08-11 - it produced the clip in the
response JSON above, the same output the hero visualization at the top of this page
is built from.)

## Tuning cost and GPU count

* **Multi-GPU bills `gpu_count × duration`.** This app runs 4× GPU-H100, so every
  runner-second costs four H100-seconds, from boot through teardown; see
  [fal pricing](https://fal.ai/pricing).
* `keep_alive=300` holds a warm four-GPU runner for 5 minutes after the last
  request: bursts share one boot, and an idle app scales to zero.
* **Changing `num_gpus`**: the Ulysses degree must divide the attention head count.
  WAN 2.2 A14B has 40 heads, so 2, 4, or 8 GPUs work. More GPUs cut per-clip latency
  at a higher per-second rate.
* **Fast previews vs final quality**: the defaults (480p, 33 frames, 6 steps) are the
  fast path; WAN's native quality settings (720p, 81 frames, 27 steps) use the same
  endpoint and the same seed contract.
* **Allocations can queue at four GPUs**: when capacity is tight, an allocation can
  wait (we have measured waits of tens of minutes at peak). This is one more reason
  to call through the queue with generous client timeouts.

Measured on real 4× GPU-H100 runs (2026-08-04 to 2026-08-11, fal 1.78.2,
diffusers 0.39.0) - one-time measurements, not a guarantee:

| Configuration | Wall-clock |
| - | - |
| 480p · 33 frames · 6 steps (defaults, warm shape) | \~62-95 s |
| 720p · 81 frames · 27 steps | \~270-282 s |

## Next steps

* [Multi-GPU Workloads](../../documentation/serverless/distributed/overview.md): the full
  strategy taxonomy - data, sequence, tensor, and hybrid parallelism.
* [Event Streaming](../../documentation/serverless/distributed/streaming.md): send preview
  frames from rank 0 with `add_streaming_result`.
* [fal.distributed API Reference](../../documentation/serverless/distributed/api-reference.md):
  `DistributedRunner` and `DistributedWorker` in depth.
* [Deploy a Text-to-Video Model](../video-generation/deploy-text-to-video-model.md):
  the single-GPU serving patterns this example builds on.

For more examples, from LoRA serving to 3D streaming, browse the
[fal-serverless-examples](https://github.com/fal-ai-community/fal-serverless-examples)
repository.
