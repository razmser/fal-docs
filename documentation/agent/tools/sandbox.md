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

# Sandbox code execution

> fal Agent runs Python in an isolated sandbox for deterministic media work: ffmpeg cuts, pixel-exact edits, format conversion, and video compositing.

Some work must not go through a generative model. A trim, a resize, a frame extraction, a caption overlay, or a format conversion must be exact and repeatable. For this, fal Agent runs Python in an isolated sandbox.

You do not need to ask for code. Say what you want, and the agent decides whether a model or the sandbox is the right tool.

```text theme={null}
Cut the first 2 seconds off the hallway clip, add the caption "Spring drop" at the bottom, and export as a square MP4.
```

## What is available

The sandbox is a Linux environment with Python and common media tools preinstalled:

* **Pillow** and **numpy** for image work
* **ffmpeg** and **ImageMagick** for video, audio, and batch image operations
* **matplotlib** for charts
* **requests** for fetching files
* **Hyperframes**, a renderer that turns HTML and CSS compositions into MP4. The [video-compositing skill](../skills.md) governs its use

The agent can install additional Python and system packages inside the sandbox when a task needs them.

## How results come back

Files the agent creates in the sandbox are uploaded to the fal CDN and appear in the chat as generation cells. The cell's pill names the kind of work, such as **video-edit**, **frame-extract**, **image-edit**, or **audio-edit**. Sandbox outputs take an ordinal like any other output. You can pick them from the palette, and they are included in [exports](../generations/export.md).

## Video compositing

The **video-compositing** skill covers styled text and graphics over footage: captions and titles, lower thirds, animated logos, kinetic typography, montages, slideshows, and picture-in-picture layouts. The agent writes an HTML composition and renders it to MP4 with Hyperframes inside the sandbox. Pure generation, such as "a video of a dragon", still goes to a video model, and a bare cut still goes to ffmpeg.

## Photo editing

The **photo-editing** skill routes tonal, geometric, and compositing edits to the sandbox. Background removal, brightness and contrast, crop, straighten, and precise compositing are done in code. Edits that change what is in the image, such as "make it winter", go to a generative edit model.

## Limits

| Limit | Value |
| :- | :- |
| Single command timeout | 300 seconds by default, up to 600 |
| Sandbox work per turn | About 210 seconds. Longer work continues in the next turn |
| Idle lifetime | 5 minutes after the last command. Files persist within a chat session while the sandbox is alive |
| Network | Outbound only. No public ports |

Each user gets their own sandbox. Sandboxes are not shared between accounts.

## Privacy

Files enter the sandbox only when the agent downloads them for a task. Results are uploaded to your fal CDN space with the same [retention](../retention.md) as any other generation.
