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

# FlashPack

> High-throughput tensor loading for PyTorch -- load models at up to 25Gbps without GDS.

[FlashPack](https://github.com/fal-ai/flashpack) is an open-source library by fal that dramatically speeds up model loading by streaming weights directly from disk to GPU with zero-copy reconstruction. Use it in your `setup()` method to reduce cold start times.

## How It Works

### 1. Flatten weights into contiguous macroblocks

At save/convert time, FlashPack takes the model's entire `state_dict`, groups tensors by data type, then flattens them into one contiguous stream of bytes. A compact weight map at the end of the file stores every parameter's key, shape, and offset.

### 2. Stream with overlapping disk, CPU, and GPU transfers

When loading, FlashPack memory-maps the file and divides it into mid-sized CPU buffers (up to 64MB each). These buffers load in a round-robin pattern, keeping disk reads continuous. Each CPU buffer is paired with a dedicated CUDA stream -- while one stream writes to VRAM, another buffer is already being loaded from disk.

### 3. Rebuild parameters as zero-copy views

Once data is on the GPU, FlashPack reconstructs each tensor as a view into the flat memory block and creates new `nn.Parameter` instances with direct references to the loaded bytes. No copies, no moves -- the model is immediately ready to run.

***

## Integration Methods

### Direct Function Calling

The simplest approach when you don't want to modify your module code:

```python theme={null}
from flashpack import pack_to_file, assign_from_file

flashpack_path = "/data/models/my-model.flashpack"
model = MyModel(...)

pack_to_file(model, flashpack_path)
assign_from_file(model, flashpack_path)
```

### PyTorch Mixin

Add `FlashPackMixin` to your module for `save_flashpack` and `from_flashpack` methods:

```python theme={null}
from flashpack import FlashPackMixin

class MyModule(nn.Module, FlashPackMixin):
    def __init__(self, some_arg: int = 4) -> None:
        ...

module = MyModule(some_arg=4)
module.save_flashpack("model.flashpack")

loaded_module = MyModule.from_flashpack("model.flashpack", some_arg=4)
```

### Diffusers Models

Use `FlashPackDiffusersModelMixin` for diffusers models with `from_pretrained_flashpack` and `save_pretrained_flashpack`:

```python theme={null}
from flashpack.integrations.diffusers import FlashPackDiffusersModelMixin
from diffusers.models import AutoencoderKL

class FlashPackAutoencoderKL(FlashPackDiffusersModelMixin, AutoencoderKL):
    pass

# Load from original weights
sdxl_vae = FlashPackAutoencoderKL.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0",
    variant="fp16",
    subfolder="vae",
)

# Save as FlashPack (optionally push to HuggingFace)
sdxl_vae.save_pretrained_flashpack(
    "/path/to/save/directory",
    push_to_hub=True,
    repo_id="my-org/sdxl-vae-flashpack",
)

# Load from FlashPack
sdxl_vae = FlashPackAutoencoderKL.from_pretrained_flashpack(
    "my-org/sdxl-vae-flashpack",
    device_map="cuda",
)
```

### Transformers Models

Works the same way with `FlashPackTransformersModelMixin`:

```python theme={null}
from flashpack.integrations.transformers import FlashPackTransformersModelMixin
from transformers import CLIPTextModel

class FlashPackCLIPTextModel(FlashPackTransformersModelMixin, CLIPTextModel):
    pass

# Load, convert, and save
clip = FlashPackCLIPTextModel.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0",
    variant="fp16",
    subfolder="text_encoder",
)
clip.save_pretrained_flashpack(
    "/path/to/save/directory",
    push_to_hub=True,
    repo_id="my-org/sdxl-text-encoder-flashpack",
)

# Load from FlashPack
clip = FlashPackCLIPTextModel.from_pretrained_flashpack(
    "my-org/sdxl-text-encoder-flashpack",
    device_map="cuda",
)
```

#### Tied-Weight Encoders (T5, UMT5)

As of `flashpack==0.4.0`, the loader assigns packed tensors by replacing each
`Parameter` object. This replacement silently breaks weight ties. For example,
`UMT5EncoderModel` ties `encoder.embed_tokens.weight` to `shared.weight`, and the
pack deliberately leaves the tied name out. After the load, the untied side keeps
its meta-tensor skeleton. A later `.to(device)`, including the one inside
diffusers' pipeline loader, then crashes with `Cannot copy out of meta tensor`.
To avoid this, load the model yourself and restore the tie through the official
embeddings API. For pipelines, pass the finished module as a keyword argument.
The loader uses a component passed that way as-is:

```python theme={null}
text_encoder = FlashPackUMT5EncoderModel.from_pretrained_flashpack(
    "/data/pack/text_encoder", device="cuda"
)
text_encoder.set_input_embeddings(text_encoder.shared)  # restore the tie
pipe = FlashPackWanPipeline.from_pretrained_flashpack(
    "/data/pack", device_map="cuda", text_encoder=text_encoder
)
```

The [Cut cold start times](../../../examples/serverless/cut-cold-starts.md) example shows
this pattern inside a full production `setup()`.

### Diffusers Pipelines

FlashPack pipelines are made of FlashPack-enabled models. You don't need to enable FlashPack on every model in a pipeline -- it automatically uses `from_pretrained_flashpack` for FlashPack-enabled models and `from_pretrained` for the rest.

```python theme={null}
from typing import Optional
from diffusers import AutoencoderKL, StableDiffusionXLPipeline, UNet2DConditionModel
from diffusers.schedulers import KarrasDiffusionSchedulers
from flashpack.integrations.diffusers import (
    FlashPackDiffusersModelMixin,
    FlashPackDiffusionPipeline,
)
from flashpack.integrations.transformers import FlashPackTransformersModelMixin
from transformers import (
    CLIPImageProcessor,
    CLIPTextModel,
    CLIPTextModelWithProjection,
    CLIPTokenizer,
    CLIPVisionModelWithProjection,
)

# Define FlashPack-enabled classes
class FlashPackCLIPTextModel(FlashPackTransformersModelMixin, CLIPTextModel):
    pass

class FlashPackCLIPTextModelWithProjection(
    FlashPackTransformersModelMixin, CLIPTextModelWithProjection
):
    pass

class FlashPackAutoencoderKL(FlashPackDiffusersModelMixin, AutoencoderKL):
    pass

class FlashPackUNet2DConditionModel(FlashPackDiffusersModelMixin, UNet2DConditionModel):
    pass

class FlashPackStableDiffusionXLPipeline(
    FlashPackDiffusionPipeline, StableDiffusionXLPipeline
):
    def __init__(
        self,
        vae: FlashPackAutoencoderKL,
        text_encoder: FlashPackCLIPTextModel,
        text_encoder_2: FlashPackCLIPTextModelWithProjection,
        tokenizer: CLIPTokenizer,
        tokenizer_2: CLIPTokenizer,
        unet: FlashPackUNet2DConditionModel,
        scheduler: KarrasDiffusionSchedulers,
        feature_extractor: Optional[CLIPImageProcessor] = None,
        image_encoder: Optional[CLIPVisionModelWithProjection] = None,
        add_watermarker: Optional[bool] = None,
        force_zeros_for_empty_prompt: bool = True,
    ) -> None:
        super().__init__(
            vae=vae,
            text_encoder=text_encoder,
            text_encoder_2=text_encoder_2,
            tokenizer=tokenizer,
            tokenizer_2=tokenizer_2,
            unet=unet,
            scheduler=scheduler,
            feature_extractor=feature_extractor,
            image_encoder=image_encoder,
            add_watermarker=add_watermarker,
            force_zeros_for_empty_prompt=force_zeros_for_empty_prompt,
        )

# Save entire pipeline as FlashPack
pipeline = FlashPackStableDiffusionXLPipeline.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0",
    variant="fp16",
)
pipeline.save_pretrained_flashpack(
    "flashpack-sdxl",
    push_to_hub=True,
    repo_id="my-org/sdxl-flashpack",
)

# Load from FlashPack -- immediately ready to use
pipeline = FlashPackStableDiffusionXLPipeline.from_pretrained_flashpack(
    "my-org/sdxl-flashpack",
    device_map="cuda",
)
image = pipeline("A sunset over mountains", num_inference_steps=50).images[0]
```

> [!NOTE]
>
> Use `Optional[]` syntax (not `| None`) for optional pipeline parameters. This is a limitation in diffusers, not FlashPack.

***

## CLI Commands

FlashPack includes a CLI for converting, inspecting, and reverting files.

### Convert a model

```bash theme={null}
# Convert a local model
flashpack convert ./my_model ./my_model.flashpack

# Convert from HuggingFace
flashpack convert stabilityai/stable-diffusion-xl-base-1.0 \
  --subfolder unet --use-diffusers

# Convert with specific dtype
flashpack convert ./my_model ./my_model.flashpack --dtype float16
```

### Inspect metadata

```bash theme={null}
flashpack metadata ./my_model.flashpack
flashpack metadata ./my_model.flashpack --show-index --json
```

### Revert to safetensors

```bash theme={null}
flashpack revert ./my_model.flashpack ./my_model.safetensors
```

***

## Using FlashPack on fal

Convert your model to FlashPack format, store it on `/data` or HuggingFace, and load in `setup()`:

```python theme={null}
import fal
from flashpack import assign_from_file
from pydantic import BaseModel

class Input(BaseModel):
    prompt: str

class MyModel(fal.App):
    machine_type = "GPU-A100"
    requirements = ["flashpack", "torch", "diffusers"]

    def setup(self):
        import torch
        from my_model import build_model

        self.model = build_model()
        assign_from_file(self.model, "/data/models/my-model.flashpack")

    @fal.endpoint("/")
    def generate(self, input: Input) -> dict:
        return self.model(input.prompt)
```

Or with the Diffusers integration:

```python theme={null}
import fal
from pydantic import BaseModel

class Input(BaseModel):
    prompt: str

class SDXLApp(fal.App):
    machine_type = "GPU-A100"
    requirements = ["flashpack", "torch", "diffusers", "transformers"]

    def setup(self):
        from my_flashpack_classes import FlashPackStableDiffusionXLPipeline

        self.pipe = FlashPackStableDiffusionXLPipeline.from_pretrained_flashpack(
            "my-org/sdxl-flashpack",
            device_map="cuda",
        )

    @fal.endpoint("/")
    def generate(self, input: Input) -> dict:
        image = self.pipe(input.prompt, num_inference_steps=30).images[0]
        return {"image": fal.toolkit.Image.from_pil(image)}
```

> [!TIP]
>
> Store FlashPack files on `/data` for automatic caching across cold starts. The first runner downloads the file, and subsequent runners load from the cached copy.

> [card] [GitHub Repository](https://github.com/fal-ai/flashpack)
>
> View source, benchmarks, and contribute on GitHub
