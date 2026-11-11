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

# Export

> Download one generation, or export every completed generation in a chat as one ZIP with a folder per batch.

## Download one file

Hover a cell and click **Download**, or use the download control in the [media viewer](media-viewer.md). The file saves directly. 3D outputs offer a format menu.

To download several tiles from the media rail, `Cmd+click` (`Ctrl+click`) to select them, then use the selection actions.

## Export a chat as a ZIP

Ask the agent to export. For example:

```text theme={null}
Export all the videos from this chat as a zip.
```

The agent builds the archive in the sandbox and shows an **export card** in the chat. The card lists the archive name, the file count, the total size, and a **Download** button. Expand it to see the folder tree.

### Archive layout

The root folder is the chat title. Inside, each named batch is a folder, and each run is a file named by its variant. Unnamed runs use their ordinal.

```text theme={null}
product-hero-shots/
  hero-shot/
    dusk.png
    morning.png
  animation/
    gen-4.mp4
  manifest.json
```

`manifest.json` lists every file with its prompt, model, and request ID.

### What is included

* Completed generations with a permanent URL.
* Filtered by batch name or media type when you ask for a subset.

Not included: your uploads, failed runs, and video sequences. A [video sequence](../tools/video-sequences.md) is an arrangement, not a file. Export the sequence first to get a file.

### Limit

One archive holds up to 200 files. For a larger chat, export by media type or by batch.
