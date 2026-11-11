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

# Browsing and previewing

> Navigate your library, adjust the media grid, and inspect images, videos, audio, and 3D assets.

## Library sections

| Section | What it shows |
| :- | :- |
| Home | An overview of recent media and your library's organization. |
| All media | Generated outputs and uploads for the selected account. |
| Generated media | Generated outputs. |
| Uploads | Media added directly to the library. |
| Favorites | Assets you have marked as favorites. |

The sidebar also opens collections and the five types of [smart entities](../entities.md).

## Adjust the grid

Change the thumbnail size to fit more media on screen or inspect larger previews. Use **Group by → Date** to separate media into date groups. In All media, you can choose newest or oldest first.

Search results use relevance ordering and are not grouped by date. Clear the search to return to ordinary browsing.

## Open an asset

Select a media tile to open the viewer. Images support a closer view. Videos and audio have playback controls. Supported 3D files have a model preview. File-format and browser support affect what can be previewed.

Use the details panel to inspect available dimensions, format, file size, creation date, model, and request ID. Generated assets can also show their prompt and related media. See [Generation details and history](history.md).

## Select several assets

Choose **Select** to enter selection mode, then select the media you want to work with. The selection toolbar provides actions such as adding to collections, applying tags, favoriting, downloading, and deleting, subject to your permissions.

Choose **Done** or press `Esc` to leave selection mode.

> [!NOTE]
>
> Removing an asset from a collection and deleting it from the library have different effects. See [Retention and deletion](../retention.md) before cleaning up a selection.
