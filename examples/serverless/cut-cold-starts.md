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

# Cut cold start times with FlashPack and compiled caches

> Serve WAN 2.1 text-to-video with weights packed on /data, FlashPack disk-to-GPU loading, and a shared torch.compile kernel cache. Measured on real deploys: steady-state cold starts drop to roughly a third of the baseline.

> [card] [View on GitHub](https://github.com/fal-ai-community/fal-serverless-examples/tree/main/02-scaling-cold-starts/cut-cold-starts)
>
> Full source for this example in fal-serverless-examples.

Every GPU cold start pays three costs before it can serve a request. It downloads the
weights, deserializes them onto the GPU, and compiles kernels for the shapes the model
will run. On WAN 2.1 text-to-video, the compile step dominates. `torch.compile`
alone takes roughly three-quarters of a from-scratch boot, more than the download.
In this example, we serve the same model twice: once with every optimization
applied, and once with none.

![FlashPack packs the state\_dict into dtype-grouped contiguous macroblocks with a weight map at the file end, then streams the file disk-to-GPU in overlapping chunks and reconstructs tensors as zero-copy views — versus safetensors' tensor-by-tensor CPU deserialization](https://mintcdn.com/fal-d8505a2e/vqZ48rZww706UN7T/images/examples/cut-cold-starts-flashpack-light.svg?fit=max\&auto=format\&n=vqZ48rZww706UN7T\&q=85\&s=4857cca7d1b2cbd863f25e2645e8ac93)

![FlashPack packs the state\_dict into dtype-grouped contiguous macroblocks with a weight map at the file end, then streams the file disk-to-GPU in overlapping chunks and reconstructs tensors as zero-copy views — versus safetensors' tensor-by-tensor CPU deserialization](https://mintcdn.com/fal-d8505a2e/vqZ48rZww706UN7T/images/examples/cut-cold-starts-flashpack-dark.svg?fit=max\&auto=format\&n=vqZ48rZww706UN7T\&q=85\&s=6927b6ef3b99b3177c5e2cd2275b6815)

## Running the Example

1. Install fal:

```bash theme={null}
pip install fal==1.78.2  # keep pinned to this example's requirements.txt
```

2. Authenticate (if not already done):

```bash theme={null}
fal auth login
```

3. Clone the examples repository and enter this example's directory:

```bash theme={null}
git clone https://github.com/fal-ai-community/fal-serverless-examples.git
cd fal-serverless-examples/02-scaling-cold-starts/cut-cold-starts
```

4. Run the app:

```bash theme={null}
fal run app.py
```

This starts an ephemeral app on a real H100 and prints its playground and API URLs.
`fal run` keeps the app alive until you press Ctrl-C.

> [!NOTE]
>
> The first run downloads the \~29 GB fp32 repo, builds the FlashPack pack, and compiles
> kernels. This takes roughly nine minutes from spawn to healthy, once per account per pack
> version. The first-boot bar in the [measured
> chart](#measuring-the-before-and-after) below shows where `setup()` spends that
> time. That measurement used fal's warm platform Hugging Face cache. A brand-new account's
> first download runs meaningfully longer. Every run after that reuses both `/data`
> artifacts, effectively bypassing the time needed for this step entirely.

To compare directly, run `fal run app_baseline.py` alongside it. The baseline app
serves the identical schema with none of the optimizations applied.

## Sharing One /data Volume Across Every Runner

This example applies three optimizations, and all three build on one shared [`/data`
volume](../../documentation/development/use-persistent-storage.md), an account-wide disk
that outlives any single runner. The first runner to boot builds each artifact once.
Every runner after it reads that artifact instead of rebuilding it. The code
comments label the three optimizations `LEVER 1` to `LEVER 3`.

**Optimization 1 — `/data` persistent storage.** The Hub repo ships fp32 weights. The first
boot for a given pack version downloads them, rebuilds the pipeline at serving
dtypes, and packs it. Every later boot skips this step entirely.

**Optimization 2 — [FlashPack](../../documentation/serverless/optimizations/flashpack.md) loading.**
FlashPack streams packed weights disk-to-GPU and rebuilds each tensor as a zero-copy
view, with no CPU deserialization step. The baseline pays safetensors' slower
tensor-by-tensor load instead. The diagram at the top of this page contrasts both
load paths. See the FlashPack doc for the packing format.

**Optimization 3 — a shared kernel cache.**
[`synchronized_inductor_cache`](../../documentation/serverless/optimizations/optimize-startup-with-compiled-caches.md)
compiles `torch.compile` kernels once per GPU type and syncs them to `/data`. Every
later boot on that GPU type unpacks the cached kernels instead of recompiling.

Kernel caches are organized per [GPU
type](../../documentation/deployment/machine-types.md). A cache built on an H100 gives an
L40 boot nothing. This app pins `machine_type` to one type, so every boot hits the
same cache and the measured numbers stay comparable. A fallback list works too.
Each GPU type then pays its own one-time compile.

From here, we walk `app.py` from top to bottom.

## Defining the Request and Response Schemas

We start with the imports and the constants that everything else in the app builds on.
`wan_flashpack` defines the four FlashPack-mixed-in classes this example needs
([`wan_flashpack.py`](https://github.com/fal-ai-community/fal-serverless-examples/blob/main/02-scaling-cold-starts/cut-cold-starts/wan_flashpack.py)
— referenced here, not walked line by line). Importing it does nothing by itself.
`setup()` calls `wan_flashpack.get_classes()`, and only then does the module build
the classes and register itself in `sys.modules`. That self-registration matters
because `local_python_modules` ships the file to the runner via cloudpickle
by-value, which reconstructs the module without a real `sys.modules` entry. Both
transformers' model loading and diffusers' pipeline loader need that entry. See
[FlashPack](../../documentation/serverless/optimizations/flashpack.md) for the packing
format these classes wrap.

`PACK_DIR` and `INDUCTOR_CACHE_KEY` both carry `PACK_VERSION`. We bump the version
whenever `MODEL_ID`, serving dtypes, or the torch/diffusers/flashpack pins change, so
new pins can never load an old pack or old kernels. `READY_MARKER` is the file that later marks both
caches complete.

```python app.py theme={null}
import random
import shutil
import tempfile
import time
import uuid
from pathlib import Path
from typing import Any, Literal

import fal
import wan_flashpack
from fal.toolkit import File, synchronized_inductor_cache
from pydantic import BaseModel, Field, field_validator

MODEL_ID = "Wan-AI/Wan2.1-T2V-1.3B-Diffusers"
# Fixed native resolution: warmup at both frame extremes then covers every
# shape the compiled transformer will ever serve.
WIDTH, HEIGHT = 832, 480
WARMUP_FRAME_COUNTS = (17, 81)
FPS = 16

# Bump when MODEL_ID, serving dtypes, or the torch pin change - a new pin
# must never load an old pack or old kernels.
PACK_VERSION = "v1"
PACK_DIR = Path(f"/data/examples/cut-cold-starts/wan21-t2v-13b-bf16-{PACK_VERSION}")
INDUCTOR_CACHE_KEY = f"cut-cold-starts-wan21-13b/{PACK_VERSION}"
STALE_BUILD_MAX_AGE_S = 3600
# Written atomically only after BOTH caches exist; a mid-setup crash leaves
# no marker, so the next boot reports itself as cache-building.
READY_MARKER = PACK_DIR / f".ready-{INDUCTOR_CACHE_KEY.replace('/', '-')}"
```

Next comes the request shape. Every `Field` carries a description, and fields with
`examples` power both the generated [playground](../../documentation/model-apis/playground.md)
form and this page's own deterministic requests below. The validator enforces WAN
2.1's frame rule: `num_frames` must satisfy `(n - 1) % 4 == 0`. `seed` is optional.
The server picks one when omitted, and echoes it back so the same seed and inputs
reproduce a comparable clip.

```python app.py theme={null}
class Input(BaseModel):
    prompt: str = Field(
        description="What to generate. WAN 2.1 rewards detailed, cinematic prompts.",
        examples=["A calico cat surfing a small wave at golden hour, cinematic"],
    )
    negative_prompt: str = Field(
        default="",
        description="What to steer away from (artifacts, styles, objects).",
    )
    num_frames: int = Field(
        default=81,
        ge=17,
        le=81,
        description="Frames at 16 fps; must be 4k+1 (17, 21, ... 81). 81 = 5 s.",
        examples=[17],
    )
    num_inference_steps: int = Field(
        default=28,
        ge=1,
        le=40,
        description="Denoising steps - the quality/latency dial.",
        examples=[8],
    )
    seed: int | None = Field(
        default=None,
        description="Random when omitted; echoed back so results reproduce.",
        examples=[42],
    )

    @field_validator("num_frames")
    @classmethod
    def _frames_are_4k_plus_1(cls, v: int) -> int:
        if (v - 1) % 4 != 0:
            raise ValueError("num_frames must be 4k+1 (17, 21, ..., 81)")
        return v
```

On the response side, we return the CDN-hosted video, the seed that produced it, and
one server-side timing. `timings.generate_s` covers denoise, VAE decode, and mp4
encode combined, and excludes the upload.

```python app.py theme={null}
class Output(BaseModel):
    video: File = Field(
        description="The generated MP4 (832x480 @ 16 fps) on the fal CDN."
    )
    seed: int = Field(description="The seed that produced this video.")
    timings: dict[str, float] = Field(
        description="Server-side stage timings, seconds (excluding upload)."
    )
```

A live deploy of this exact code returned the response below for a fast, cheap
request (`num_frames=17`, `num_inference_steps=8`, `seed=42`). fal CDN links have
limited retention, so treat the `url` as illustrative, not durable:

```json theme={null}
{
  "video": {
    "url": "https://v3b.fal.media/files/b/0aa6e11b/X66pQEc39pD8YXfGiylYB_video.mp4",
    "content_type": "video/mp4",
    "file_name": "video.mp4",
    "file_size": 121597
  },
  "seed": 42,
  "timings": {"generate_s": 6.41}
}
```

Finally, one more schema exists outside the generation contract. `/setup-report`
is a diagnostic endpoint. It returns what a given runner's `setup()` spent its time
on: which variant produced it, which GPU it booted on, whether this boot built the
caches or found them warm, and the per-stage timings. Ephemeral runners expose only
coarse scheduling states over the platform API, so the per-stage figures in the
[measured cold starts](#measuring-the-before-and-after) chart below come from these
in-app timers, not from the platform. The spawn-to-healthy times in the chart's
disclosures are the one exception, because no in-app timer can see them. The
example's `bench.py` measures those from outside, as wall-clock time from deploy
to first healthy response.

```python app.py theme={null}
class SetupReportInput(BaseModel):
    """No parameters - POST an empty JSON object."""


class SetupReportOutput(BaseModel):
    variant: Literal["baseline", "optimized"] = Field(
        description="Which app variant produced this report."
    )
    gpu: str = Field(description="GPU this worker booted on.")
    built_caches_this_boot: bool = Field(
        description="True when this boot found no readiness marker and built "
        "cache state (/data pack and/or compiled kernels). Steady-state boots "
        "find the marker, reuse both, and report False."
    )
    timings: dict[str, float] = Field(
        description="Per-stage setup timings in order, seconds."
    )
    total_seconds: float = Field(description="Total setup() wall-clock time.")
```

## Configuring the App and Pinning One GPU

With the schemas defined, we can turn to the app class itself. `machine_type =
"GPU-H100"` pins exactly one [GPU type](../../documentation/deployment/machine-types.md)
rather than a fallback list, for the cache-consistency reasons described above.
`local_python_modules = ["wan_flashpack"]` ships that file to the runner.
`startup_timeout = 2700` covers the slowest boot this app will ever see, the
account's first-ever run. That run downloads, packs, and compiles for real.
`requirements` is the authoritative dependency list fal installs at deploy time.
`requirements.txt` mirrors it exactly for local runs and CI.

```python app.py theme={null}
class ColdStartOptimized(fal.App, keep_alive=60):
    machine_type = "GPU-H100"  # pin ONE type: inductor caches are per GPU type
    app_auth = "private"
    startup_timeout = (
        2700  # covers the account's first-ever boot (download + pack + compile)
    )
    local_python_modules = ["wan_flashpack"]
    requirements = [
        "fal==1.78.2",  # keep in sync with harness/requirements.txt
        "flashpack==0.4.0",
        "diffusers==0.39.0",
        "transformers==5.3.0",
        "torch==2.12.1",
        "accelerate==1.14.0",
        "imageio==2.37.4",
        "imageio-ffmpeg==0.6.0",
    ]
```

`setup()` times every stage with a small `stage()` helper and reports all of them
through `/setup-report`. The first stage calls `wan_flashpack.get_classes()`, which
builds the four FlashPack classes and installs the `sys.modules` entry described
above. This call is the only place in `app.py` where that import actually resolves.

```python app.py theme={null}
    def setup(self) -> None:
        """Pack once to /data, stream via FlashPack, compile against the
        shared cache."""
        timings: dict[str, float] = {}
        setup_start = time.perf_counter()

        def stage(name: str, start: float) -> None:
            timings[name] = round(time.perf_counter() - start, 2)

        t0 = time.perf_counter()
        import torch

        # Also installs the sys.modules entry that transformers introspection
        # and the pack's model_index.json class resolution both need.
        classes: dict[str, type[Any]] = wan_flashpack.get_classes()
        FlashPackWanPipeline = classes["FlashPackWanPipeline"]
        stage("imports", t0)
```

## Packing the Model onto /data, Once

Now the first optimization. `self.built_caches_this_boot` records whether
`READY_MARKER` was missing when this runner booted. `/setup-report` surfaces this
flag, so a caller can tell a cache-building boot from a steady-state one. When
`PACK_DIR` does not exist, this runner downloads the fp32 repo, rebuilds the
pipeline at serving dtypes (bf16 transformer and text encoder, fp32 VAE), and packs
it with FlashPack's `save_pretrained_flashpack`. The runner writes the pack to
**local disk first**, then publishes it to `/data` with one bulk copy and an atomic
rename. Chunked writes straight to `/data` are very slow. The atomic rename keeps
concurrently booting runners from reading a half-written pack. The runner that
loses the race cleans up its own copy.

```python app.py theme={null}
        self.built_caches_this_boot = not READY_MARKER.is_file()

        # LEVER 1 - first boot per ACCOUNT: build the pack on LOCAL disk, then
        # publish as one bulk copy + atomic rename (chunked writes straight to
        # /data are pathologically slow; the rename keeps concurrent boots
        # from ever seeing a half-written pack).
        if not PACK_DIR.is_dir():
            t0 = time.perf_counter()
            from diffusers.schedulers import UniPCMultistepScheduler
            from huggingface_hub import snapshot_download
            from transformers import AutoTokenizer

            repo_dir = Path(snapshot_download(MODEL_ID))
            transformer = classes["FlashPackWanTransformer3DModel"].from_pretrained(
                repo_dir / "transformer", torch_dtype=torch.bfloat16
            )
            vae = classes["FlashPackAutoencoderKLWan"].from_pretrained(
                repo_dir / "vae", torch_dtype=torch.float32
            )
            text_encoder = classes["FlashPackUMT5EncoderModel"].from_pretrained(
                repo_dir / "text_encoder", torch_dtype=torch.bfloat16
            )
            build_pipe = FlashPackWanPipeline(
                tokenizer=AutoTokenizer.from_pretrained(repo_dir / "tokenizer"),
                text_encoder=text_encoder,
                vae=vae,
                transformer=transformer,
                scheduler=UniPCMultistepScheduler.from_pretrained(
                    repo_dir / "scheduler"
                ),
            )
            local_dir = Path(tempfile.mkdtemp(prefix="wan-flashpack-build-")) / "pack"
            local_dir.mkdir(parents=True)
            build_pipe.save_pretrained_flashpack(str(local_dir))
            del build_pipe, transformer, vae, text_encoder
            stage("download + build FlashPack pack (first boot only)", t0)

            t0 = time.perf_counter()
            PACK_DIR.parent.mkdir(parents=True, exist_ok=True)
            for stale in PACK_DIR.parent.glob(".build-*"):  # crashed builds
                if time.time() - stale.stat().st_mtime > STALE_BUILD_MAX_AGE_S:
                    shutil.rmtree(stale, ignore_errors=True)
            publish_dir = PACK_DIR.parent / f".build-{uuid.uuid4().hex}"
            shutil.copytree(local_dir, publish_dir)
            try:
                publish_dir.rename(PACK_DIR)
            except OSError:
                shutil.rmtree(publish_dir, ignore_errors=True)
                if not PACK_DIR.is_dir():
                    # Nobody else published: real storage failure, not a lost race.
                    raise
            shutil.rmtree(local_dir.parent, ignore_errors=True)
            stage("publish pack to /data (first boot only)", t0)
```

## Loading 15 GB in Seconds with FlashPack

Every boot, first or steady-state, reaches this stage.
`from_pretrained_flashpack` streams the pack from `/data` to the GPU directly, with
no CPU deserialization step. This is the
[FlashPack](../../documentation/serverless/optimizations/flashpack.md) loading path in the
diagram at the top of this page. One integration caveat applies here, as of
`flashpack==0.4.0` (the version this example pins). transformers ties
`UMT5EncoderModel`'s `encoder.embed_tokens.weight` to `shared.weight`. FlashPack's
loader assigns packed tensors by replacing each `Parameter` object, which silently
breaks that tie (the pack deliberately omits the tied name). `setup()` loads the
text encoder itself, restores the tie through the official `set_input_embeddings`
API, and hands the finished module to the pipeline loader as a keyword argument.
The loader uses a component passed that way as-is and skips the broken path. The
loop right after checks every component for leftover meta tensors and fails loudly
instead of crashing later with an opaque device error.

```python app.py theme={null}
        # LEVER 2 - every boot: stream the pack /data -> GPU.
        t0 = time.perf_counter()
        # flashpack's loader REPLACES Parameter objects, silently breaking the
        # UMT5 embed_tokens/shared weight tie (the tied name is left out of
        # the pack), and the pipeline loader's .to() then crashes on the
        # leftover meta tensor. Load the text encoder ourselves, restore the
        # tie, and pass it as a kwarg - supplied components are used as-is.
        text_encoder = classes["FlashPackUMT5EncoderModel"].from_pretrained_flashpack(
            str(PACK_DIR / "text_encoder"), device="cuda"
        )
        text_encoder.set_input_embeddings(text_encoder.shared)
        pipe = FlashPackWanPipeline.from_pretrained_flashpack(
            str(PACK_DIR), device_map="cuda", text_encoder=text_encoder
        )
        for component_name in ("transformer", "vae", "text_encoder"):
            module = getattr(pipe, component_name)
            leftover = [
                name
                for name, tensor in (
                    # remove_duplicate=False: dedup would mask a broken tie.
                    list(module.named_parameters(remove_duplicate=False))
                    + list(module.named_buffers(remove_duplicate=False))
                )
                if tensor.is_meta
            ]
            # Must run BEFORE any .to(): meta tensors ride through .to() unchanged.
            if leftover:
                raise RuntimeError(
                    f"{component_name}: tensors not materialized from pack: "
                    f"{leftover[:5]}"
                )
        pipe.set_progress_bar_config(disable=True)
        self.pipe = pipe
        stage("load weights (FlashPack, /data -> GPU)", t0)
```

## Compiling Once, Reusing the Kernels Everywhere

The third optimization wraps `torch.compile(self.pipe.transformer, dynamic=True)` inside
[`synchronized_inductor_cache`](../../documentation/serverless/optimizations/optimize-startup-with-compiled-caches.md).
The first boot for `INDUCTOR_CACHE_KEY` compiles for real and syncs the kernels to
`/data` when the context exits. Every later boot on the same GPU type unpacks those
kernels instead of recompiling, and the warmup loop becomes a cache-hit execution
instead of a compile. Warmup must run inside the context. Kernels compiled outside
it are never captured.

```python app.py theme={null}
        # LEVER 3 - compile against the shared inductor cache. Warmup MUST
        # run inside the context or nothing is captured.
        t0 = time.perf_counter()
        with synchronized_inductor_cache(INDUCTOR_CACHE_KEY):
            self.pipe.transformer = torch.compile(self.pipe.transformer, dynamic=True)
            for frames in WARMUP_FRAME_COUNTS:
                self.pipe(
                    prompt="warmup",
                    height=HEIGHT,
                    width=WIDTH,
                    num_frames=frames,
                    num_inference_steps=1,
                    output_type="latent",
                    generator=torch.Generator("cuda").manual_seed(0),
                )
        stage("compile + warmup (shared inductor cache)", t0)
```

With all three optimizations in place, one thing is still missing: a way to know
whether both caches are complete. `READY_MARKER` answers that question. `setup()`
writes it atomically, through a temp file and a rename, only after both the `/data`
pack and the synced kernel cache exist. A runner that crashes mid-`setup()` leaves
no marker, so the next boot finds an incomplete cache and rebuilds it instead of
trusting a half-finished one. The final three lines record this boot's per-stage
timings, its total `setup()` time, and its GPU name. All three feed
`/setup-report`.

```python app.py theme={null}
        if self.built_caches_this_boot:
            marker_tmp = READY_MARKER.with_name(
                f"{READY_MARKER.name}.tmp-{uuid.uuid4().hex}"
            )
            marker_tmp.write_text("ok")
            marker_tmp.rename(READY_MARKER)

        self.setup_timings = timings
        self.setup_total = round(time.perf_counter() - setup_start, 2)
        self.gpu_name = torch.cuda.get_device_name(0)
```

## Serving Requests

With `setup()` done, two endpoints are left. `generate` is the endpoint every
request hits. It denoises at the caller's frame count and step count, encodes the
frames to mp4, and uploads the file to fal's CDN.

```python app.py theme={null}
    @fal.endpoint("/")
    def generate(self, request: Input) -> Output:
        import torch
        from diffusers.utils import export_to_video

        seed = (
            request.seed if request.seed is not None else random.randint(0, 2**32 - 1)
        )
        t0 = time.perf_counter()
        frames = self.pipe(
            prompt=request.prompt,
            negative_prompt=request.negative_prompt,
            height=HEIGHT,
            width=WIDTH,
            num_frames=request.num_frames,
            num_inference_steps=request.num_inference_steps,
            generator=torch.Generator("cuda").manual_seed(seed),
        ).frames[0]
        with tempfile.TemporaryDirectory() as tmp:
            path = Path(tmp) / "video.mp4"
            export_to_video(frames, str(path), fps=FPS)
            timings = {"generate_s": round(time.perf_counter() - t0, 2)}
            video = File.from_path(path, content_type="video/mp4")
        return Output(video=video, seed=seed, timings=timings)
```

`setup_report` is the diagnostic endpoint behind the [measured cold
starts](#measuring-the-before-and-after) chart further down this page. It returns
this runner's timings without running any generation.

```python app.py theme={null}
    @fal.endpoint("/setup-report")
    def setup_report(self, request: SetupReportInput) -> SetupReportOutput:
        """What this worker's cold start actually spent its time on."""
        return SetupReportOutput(
            variant="optimized",
            gpu=self.gpu_name,
            built_caches_this_boot=self.built_caches_this_boot,
            timings=self.setup_timings,
            total_seconds=self.setup_total,
        )
```

## Deploying to Production

```bash theme={null}
fal deploy app.py
```

This registers `cold-start-optimized`. To test its endpoints in a browser, follow
[Testing Your Own Apps](../../documentation/model-apis/playground.md#your-own-apps).
fal derives the registered name from the `App` class name,
`ColdStartOptimized`, not from the filename. Video generation takes tens of seconds
to minutes at production settings, so always call the endpoint through the [queue
API](../../documentation/model-apis/inference/queue.md), never through raw synchronous HTTP.

**Python**

```bash theme={null}
pip install fal-client
```

```python theme={null}
import fal_client

result = fal_client.subscribe(
    "<your-username>/cold-start-optimized",
    arguments={
        "prompt": "A calico cat surfing a small wave at golden hour, cinematic",
        "num_frames": 17,
        "num_inference_steps": 8,
        "seed": 42,
    },
)
print(result["video"]["url"])
```

**JavaScript**

```javascript theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("<your-username>/cold-start-optimized", {
  input: {
    prompt: "A calico cat surfing a small wave at golden hour, cinematic",
    num_frames: 17,
    num_inference_steps: 8,
    seed: 42,
  },
});
console.log(result.data.video.url);
```

**curl (queue API)**

```bash theme={null}
curl -X POST "https://queue.fal.run/<your-username>/cold-start-optimized" \
  -H "Authorization: Key $FAL_KEY" -H "Content-Type: application/json" \
  -d '{"prompt": "A calico cat surfing a small wave at golden hour, cinematic", "num_frames": 17, "num_inference_steps": 8, "seed": 42}'
# Returns {"request_id": "...", "status_url": "...", ...}: poll the status_url, then fetch the result
```

## Measuring the Before and After

Here is what a real measured deploy of both apps looked like:

![Stacked bar chart of measured cold-start stage timings on GPU-H100 (2026-08-12). Baseline setup() takes 275.25 s: imports 26.26, weights 46.09, compile + warmup 202.9. The optimized first boot, one-time per account per pack version, takes 467.95 s: imports 54.61, weights (download, pack, publish, load) 214.49, compile + warmup 198.75. The optimized steady state takes 96.46 s, roughly 3x faster than the baseline: imports 11.0, FlashPack weights load 7.66, cached compile + warmup 77.79.](https://mintcdn.com/fal-d8505a2e/vqZ48rZww706UN7T/images/examples/cut-cold-starts-timings-light.svg?fit=max\&auto=format\&n=vqZ48rZww706UN7T\&q=85\&s=b2546aae01168399d3c36c560b4bbe90)

![Stacked bar chart of measured cold-start stage timings on GPU-H100 (2026-08-12). Baseline setup() takes 275.25 s: imports 26.26, weights 46.09, compile + warmup 202.9. The optimized first boot, one-time per account per pack version, takes 467.95 s: imports 54.61, weights (download, pack, publish, load) 214.49, compile + warmup 198.75. The optimized steady state takes 96.46 s, roughly 3x faster than the baseline: imports 11.0, FlashPack weights load 7.66, cached compile + warmup 77.79.](https://mintcdn.com/fal-d8505a2e/vqZ48rZww706UN7T/images/examples/cut-cold-starts-timings-dark.svg?fit=max\&auto=format\&n=vqZ48rZww706UN7T\&q=85\&s=a7af04f92e0f47e4cb47ee067c57e3a3)

Three disclosures apply to this chart:

* **A warm Hugging Face cache.** Two accounts produced these numbers: one ran the
  baseline and steady-state measurements, and a second ran the dedicated first
  boot. Both had a warm `/data`-backed platform HF cache on every download stage.
  The pack build (179.79 s) and the baseline weights segment reflect that cache,
  not a genuinely cold network fetch. A brand-new account sees those stages run
  meaningfully higher.
* **Allocation contention, not model work.** The steady-state `spawn -> healthy`
  spread (114.7 s to 267.3 s) came from GPU-allocation contention on a shared
  account. The model work itself was stable.
* **The first boot repeats.** The first-boot bar is one-time per account per
  pack version. The next `PACK_VERSION` bump, or a brand-new account, pays it
  again.

These are one-time measurements, not a guarantee. The example repo's
[`bench.py`](https://github.com/fal-ai-community/fal-serverless-examples/blob/main/02-scaling-cold-starts/cut-cold-starts/bench.py)
reproduces these numbers against your own account, one real ephemeral deploy per run.

## Tuning Cost and Keep-Alive

* **Both apps bill from `setup()` start through teardown.** A cold start is not
  free just because no request has landed yet. See [fal
  pricing](https://fal.ai/pricing).
* **`keep_alive=60`** holds a warm runner for 60 seconds after the last request.
  Raise it, or set `min_concurrency`, to cut cold starts further on traffic with
  longer gaps. See [Adjust Scaling
  Parameters](../../documentation/serverless/optimizations/cold-start-scaling.md).
* **Bump `PACK_VERSION`** whenever `MODEL_ID`, serving dtypes, or the
  torch/diffusers/flashpack pins change. A stale pack or kernel cache built under
  old pins must never load under new ones.
* **When to skip these optimizations:**

  * the model has little or no `torch.compile` step
  * the app rarely scales from zero, because a high `keep_alive` or `min_concurrency` solves that more simply
  * the model is small and a cold start already takes a few seconds

  See [Optimize Cold Starts](../../documentation/serverless/optimizations/optimize-cold-starts.md) for the general playbook.

## Next Steps

* [FlashPack](../../documentation/serverless/optimizations/flashpack.md): the packing
  format optimization 2 builds on.
* [Optimize Startup with Compiled
  Caches](../../documentation/serverless/optimizations/optimize-startup-with-compiled-caches.md):
  `synchronized_inductor_cache` in depth.
* [Optimize Cold Starts](../../documentation/serverless/optimizations/optimize-cold-starts.md):
  the general cold-start playbook this example specializes.
* [Adjust Scaling
  Parameters](../../documentation/serverless/optimizations/cold-start-scaling.md):
  `keep_alive` and `min_concurrency`, the settings that avoid cold starts entirely.
* [Persistent Storage](../../documentation/development/use-persistent-storage.md): the
  `/data` volume both cached artifacts live on.

For more examples, from LoRA serving to 3D streaming, browse the
[fal-serverless-examples](https://github.com/fal-ai-community/fal-serverless-examples)
repository.
