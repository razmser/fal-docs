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

# Generations and the media rail

> How generations appear in the chat and the media rail, and what you can do with each tile.

A generation is one model run's output. Generations appear twice: inline in the chat, at the point where the agent submitted them, and in the media rail on the right.

## In the chat

Runs render as cells in a grid. A batch renders as one grid, laid out like a moodboard. In-flight cells show the model name and a cancel button. Completed cells show the media, a media-type badge, and on hover:

| Action | Where |
| :- | :- |
| **Retry** | Top left. Runs the same input again |
| **Download** | Top right. 3D outputs offer a format menu |
| **Copy prompt** | Copies the prompt text |
| **Copy input as JSON** | Copies the full model input |

A toolbar also appears under a hovered cell. It offers **Attach**, **Quick animate**, **Upscale**, **Make a variation**, **Edit**, and **Details**.

![A generation cell with its ordinal and model pill, and the hover toolbar beneath it](https://mintcdn.com/fal-d8505a2e/DmEWlE6WV9vaz0Me/images/agent/tile-actions.jpg?fit=max\&auto=format\&n=DmEWlE6WV9vaz0Me\&q=85\&s=c08e7970114284e2a614d5e3cabf6d77)

Videos play a preview on hover. Audio renders as a waveform tile. Files that are not media, such as a `.zip` or `.csv`, render as a file card with a download button.

Retries are grouped in the same cell. Use **Previous attempt** and **Next attempt** to compare them.

![A batch of four generations in the chat, with the download button and model pill shown on the hovered cell](https://mintcdn.com/fal-d8505a2e/DmEWlE6WV9vaz0Me/images/agent/generation-grid.jpg?fit=max\&auto=format\&n=DmEWlE6WV9vaz0Me\&q=85\&s=8e5d07e49b92cb034ba00c44347cf59a)

## Names and ordinals

Each completed output gets an ordinal in the order the runs landed. Your uploads and video sequences do not take an ordinal. When the agent names a batch, each run also gets a readable name, such as `cdg-bag-environments/open-field`. The media viewer header shows both. Use the **Generations** section of the `@` palette to refer to an output in your next message.

## The media rail

The rail shows every tile in the chat, or in the whole project when the scope is **Project**. Filter by media type or **Favorites**, and search by prompt, model, or name.

![The media rail with Media and Context tabs, search, media-type filters, and the Attachments and Outputs sections](https://mintcdn.com/fal-d8505a2e/DmEWlE6WV9vaz0Me/images/agent/media-rail.jpg?fit=max\&auto=format\&n=DmEWlE6WV9vaz0Me\&q=85\&s=13c4ce3ce90c35bd692c6a8912c996d5)

Tile actions, on hover or in the context menu:

| Action | Effect |
| :- | :- |
| **Attach to chat** | Adds the tile as an attachment on your next message |
| **Favorite** | Pins a heart on the tile. Favorites get their own filter |
| **Preview** | Opens the [media viewer](media-viewer.md) |
| **Jump to chat** | Scrolls the chat to the turn that made it |
| **Remove from chat** | Removes the tile from this chat. This asks for confirmation and cannot be undone |

`Cmd+click` (`Ctrl+click`) selects several tiles. An **Attach N** button then attaches all of them.

## Cost

The spend chip next to the composer shows the number of generations and the total cost of the chat. Hover it for a breakdown by model or by media type. See [Spending caps](../spending-caps.md).

## Request details

Open a tile's details to see the model, the endpoint, the request ID, the latency, and the attempt number. Use the request ID when you contact support about a run.

## Errors

A failed cell shows a short explanation. The agent repairs and retries most failures on its own. See [Models and generation](../models-and-generation.md#errors-you-can-see) for the messages and what they mean.
