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
> - [Pricing](../../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Media viewer

> Open any generation full screen, zoom, inspect the request, mark it up, and send an edit straight back to the agent.

Click a tile to open the media viewer. It shows one generation at full size with a filmstrip of the others in the chat. The header shows the ordinal, the name, the format, and the dimensions.

![The media viewer with the filmstrip, the image toolbar on the left, and the edit composer at the bottom](https://mintcdn.com/fal-d8505a2e/DmEWlE6WV9vaz0Me/images/agent/media-viewer.jpg?fit=max\&auto=format\&n=DmEWlE6WV9vaz0Me\&q=85\&s=5010b19e775ef8318c35d040d9c1f8dc)

## Controls

| Control | Action |
| :- | :- |
| Previous and next | Move through the filmstrip |
| Zoom | Zoom in and out on images. The reset control returns to 100% |
| Favorite | Toggle the heart |
| Request details | Open a panel with the endpoint, request ID, status, attempt, and duration. Use **Copy request id** when you contact support |
| Close | Return to the chat. `Escape` also closes |

## Edit from the viewer

The composer at the bottom of the viewer sends an instruction to the agent with the current image attached. Type an edit, or ask for a variation, without leaving the viewer.

The left toolbar offers quick actions for image outputs.

* **Edit** opens the markup canvas. See below.
* **Quick animate** turns the still into a short video.
* **Upscale** runs an upscaler on the image.
* **Make a variation** asks for one or more new takes. A picker sets the count.

## Markup and crop

**Edit** opens a drawing canvas over the image. Use it to point at the region you want changed.

![The markup canvas with pen, highlighter, eraser, arrow, text, and crop tools, and a red stroke drawn on the image](https://mintcdn.com/fal-d8505a2e/DmEWlE6WV9vaz0Me/images/agent/markup-canvas.jpg?fit=max\&auto=format\&n=DmEWlE6WV9vaz0Me\&q=85\&s=4a7adb71a4869e98484fe140375a5313)

| Tool | Use |
| :- | :- |
| Pen and highlighter | Draw on the image. Pick a color from the swatch |
| Eraser | Remove a stroke |
| Arrow | Point at a region |
| Text | Label a region |
| Crop | Keep a region of the image |
| Undo, redo, clear | Step back or start over |

Click **Done**. The canvas is flattened to a PNG and placed in your composer as an attachment. Type what you want changed and send. The agent uses the marked image as the edit source. **Cancel** discards the marks.
