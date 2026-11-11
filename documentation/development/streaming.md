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

# Streaming Endpoints

> Stream progressive results to clients using Server-Sent Events (SSE) for real-time feedback during long-running operations.

Streaming endpoints let you send progressive results to clients as your model processes a request, rather than waiting for the full result. This is essential for long-running operations where users benefit from seeing incremental progress: image generation previews at each diffusion step, intermediate 3D geometry, or token-by-token LLM output. Callers consume the stream using the [`stream()` method](../model-apis/inference/streaming.md) in the fal client SDKs.

Under the hood, streaming uses [Server-Sent Events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events) (SSE), a one-way protocol where the server pushes events to the client over a single HTTP connection. You define a streaming endpoint by returning a FastAPI `StreamingResponse` with SSE-formatted events from an `@fal.endpoint("/stream")` method. For bidirectional communication where clients send multiple inputs over a persistent connection, see [Realtime Endpoints](realtime.md) instead.

## Streaming vs Realtime

Streaming and realtime endpoints serve different interaction patterns. Streaming is one-way (server to client) and suited for progressive output from a single request. Realtime is bidirectional (client and server) and suited for interactive applications with back-to-back requests over a persistent WebSocket connection.

| Feature | Streaming (SSE) | Realtime (WebSocket) |
| - | - | - |
| **Direction** | One-way (server to client) | Bidirectional |
| **Connection** | New connection per request | Persistent, reusable |
| **Best for** | Progressive output, previews | Interactive apps, back-to-back requests |
| **Protocol** | JSON over SSE | Binary msgpack |

Choose streaming when you want to show generation progress (diffusion steps, [progressive 3D rendering](../../examples/video-generation/deploy-3d-progressive-rendering.md)) or stream LLM tokens, and the client sends a single request per generation. Choose [realtime](realtime.md) when users interact continuously, like drawing on a canvas that triggers re-generation on every stroke.

## Example: Streaming Intermediate Steps with SDXL

This example shows how to stream intermediate image previews during Stable Diffusion XL generation. It uses a TinyVAE for fast preview decoding and the pipeline's callback system to capture progress at each step.

```python theme={null}
import fal
import json
import base64
import random
from io import BytesIO
from typing import Any
from queue import Queue

import torch
from pydantic import BaseModel, Field
from fastapi.responses import StreamingResponse


class StreamInput(BaseModel):
    prompt: str = Field(description="The prompt to generate an image from.")
    negative_prompt: str = Field(default="blurry, low quality")
    num_inference_steps: int = Field(default=20, ge=1, le=50)
    width: int = Field(default=1024)
    height: int = Field(default=1024)
    seed: int | None = Field(default=None)


class StreamingSDXLApp(fal.App):
    machine_type = "GPU-A100"

    requirements = [
        "accelerate==1.4.0",
        "diffusers==0.30.3",
        "torch==2.6.0+cu124",
        "transformers==4.47.1",
        "--extra-index-url",
        "https://download.pytorch.org/whl/cu124",
    ]

    def setup(self):
        from diffusers import AutoencoderTiny, StableDiffusionXLPipeline

        # Load the main SDXL pipeline
        self.pipeline = StableDiffusionXLPipeline.from_pretrained(
            "stabilityai/stable-diffusion-xl-base-1.0",
            torch_dtype=torch.float16,
            variant="fp16",
        ).to("cuda")

        # TinyVAE for fast preview generation (much faster than full VAE)
        self.tiny_vae = AutoencoderTiny.from_pretrained(
            "madebyollin/taesdxl",
            torch_dtype=torch.float16,
        ).to("cuda")

    @fal.endpoint("/stream")
    def stream_image(self, input: StreamInput) -> StreamingResponse:
        seed = input.seed if input.seed is not None else random.randint(0, 2**32 - 1)
        generator = torch.Generator(device="cuda").manual_seed(seed)

        # Queue to pass events from callback to the streaming generator
        event_queue: Queue[dict[str, Any] | None] = Queue()

        def pipeline_callback(
            pipe: Any,
            step: int,
            timestep: int,
            callback_kwargs: dict[str, Any],
        ) -> dict[str, Any]:
            """Called after each inference step to stream preview."""
            # Only stream every 5 steps to reduce overhead
            if step > 0 and step % 5 != 0:
                return callback_kwargs

            latents = callback_kwargs["latents"]

            # Decode latents to image using TinyVAE (fast!)
            with torch.no_grad():
                image = self.tiny_vae.decode(
                    latents / self.tiny_vae.config.scaling_factor,
                    return_dict=False,
                )[0]
                image = self.pipeline.image_processor.postprocess(
                    image, output_type="pil"
                )[0]

            # Convert to base64 data URI
            buffer = BytesIO()
            image.save(buffer, format="JPEG", quality=70)
            data_uri = f"data:image/jpeg;base64,{base64.b64encode(buffer.getvalue()).decode()}"

            # Stream the image in the format the playground expects
            event_queue.put({
                "image": {
                    "url": data_uri,
                    "content_type": "image/jpeg",
                }
            })

            return callback_kwargs

        def event_stream():
            import threading

            def run_pipeline():
                # Run the pipeline with our callback
                result = self.pipeline(
                    prompt=input.prompt,
                    negative_prompt=input.negative_prompt,
                    width=input.width,
                    height=input.height,
                    num_inference_steps=input.num_inference_steps,
                    generator=generator,
                    callback_on_step_end=pipeline_callback,
                )

                # Get final image
                final_image = result.images[0]
                buffer = BytesIO()
                final_image.save(buffer, format="JPEG", quality=95)
                data_uri = f"data:image/jpeg;base64,{base64.b64encode(buffer.getvalue()).decode()}"

                # Send final result in the same format
                event_queue.put({
                    "image": {
                        "url": data_uri,
                        "content_type": "image/jpeg",
                    }
                })
                event_queue.put(None)  # Signal completion

            # Run pipeline in background thread
            thread = threading.Thread(target=run_pipeline)
            thread.start()

            # Yield events as they come in
            while True:
                event = event_queue.get()
                if event is None:
                    break
                yield f"data: {json.dumps(event)}\n\n"

            thread.join()

        return StreamingResponse(
            event_stream(),
            media_type="text/event-stream",
        )

    @fal.endpoint("/")
    def generate(self, input: StreamInput) -> dict[str, Any]:
        """Standard non-streaming endpoint."""
        seed = input.seed if input.seed is not None else random.randint(0, 2**32 - 1)
        generator = torch.Generator(device="cuda").manual_seed(seed)

        result = self.pipeline(
            prompt=input.prompt,
            negative_prompt=input.negative_prompt,
            width=input.width,
            height=input.height,
            num_inference_steps=input.num_inference_steps,
            generator=generator,
        )

        return {"image": result.images[0], "seed": seed}
```

### Example Details

This example uses `madebyollin/taesdxl`, a TinyVAE that decodes intermediate latents roughly 10x faster than the full VAE. The diffusers `callback_on_step_end` hook captures latents at each denoising step, but the callback only streams every 5 steps to balance responsiveness with overhead. A thread-safe queue passes events from the pipeline thread (which runs inference) to the streaming generator (which yields SSE events to the client).

> [!TIP]
>
> **Playground Image Display Format**
>
> For images to display correctly in the fal playground, you **must** include both `url` and `content_type`:
>
> ```python theme={null}
> {"image": {"url": "data:image/jpeg;base64,...", "content_type": "image/jpeg"}}
> ```
>
> The playground uses `content_type` to determine how to render the result. Without it, the result will be displayed as raw JSON instead of an image.
>
> For multiple images, use an array:
>
> ```python theme={null}
> {"images": [{"url": "...", "content_type": "image/jpeg"}, ...]}
> ```

## Client-Side Usage

> [!NOTE]
>
> **Endpoint Path Requirement**
>
> The `fal_client.stream()` (Python) and `fal.stream()` (JavaScript) functions automatically append `/stream` to your endpoint ID. This means your app **must** define a streaming endpoint at `/stream` using `@fal.endpoint("/stream")`.
>
> For example, calling `fal_client.stream("your-username/your-app-name", ...)` will connect to `https://fal.run/your-username/your-app-name/stream`.

```python Python theme={null}
import fal_client

for event in fal_client.stream(
    "your-username/your-app-name",
    arguments={"prompt": "a beautiful sunset", "num_inference_steps": 20},
):
    image_url = event.get("image", {}).get("url")
    if image_url:
        print(f"Received image preview")
```

```javascript JavaScript theme={null}
import { fal } from "@fal-ai/client";

const stream = await fal.stream("your-username/your-app-name", {
  input: {
    prompt: "a beautiful sunset",
    num_inference_steps: 20,
  },
});

for await (const event of stream) {
  if (event.image?.url) {
    console.log("Received image preview");
  }
}

const finalResult = await stream.done();
```

## Key Points

Your streaming endpoint must return a FastAPI `StreamingResponse` with `media_type="text/event-stream"`. Each event is yielded as `f"data: {json.dumps(payload)}\n\n"` (note the double newline, which is part of the SSE spec). For images to display in the Playground, include both `url` and `content_type` in the event payload. Throttle your streaming to avoid sending every intermediate result, and use lower quality or resolution for previews to save bandwidth.

## Next Steps

> [card] [Realtime Endpoints](realtime.md)
>
> Bidirectional WebSocket communication for interactive apps

> [card] [3D Progressive Rendering](../../examples/video-generation/deploy-3d-progressive-rendering.md)
>
> Stream voxel data in real-time during 3D diffusion

> [card] [Distributed Streaming](../serverless/distributed/streaming.md)
>
> Stream results from multi-GPU workers
