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

# Video sequences

> Arrange clips into an editable timeline with audio layers, adjust it by hand or through the agent, and export one video file.

A video sequence is an ordered cut of clips with audio layers. The agent proposes it, you refine it, and nothing is rendered until you export. This keeps every change instant.

## Turn on the sequencer

Sequences are off by default. Turn on **Settings → General → Enable sequencer**. When the sequencer is off, the agent renders combined videos to a file in the sandbox instead.

## Create a sequence

Ask for a cut once you have clips in the chat:

```text theme={null}
Sequence the field shot, the hallway shot, and the stairwell shot in that order. Add the music track under it at low volume.
```

The agent creates a **sequence card** in the chat. The card plays the clips back to back with play, pause, mute, and seek controls, and an **Export** button.

## The sequence editor

Open the card to edit the sequence full screen.

| Area | What you can do |
| :- | :- |
| **Timeline** | Reorder shots by dragging. Trim each shot with its handles. Remove a shot |
| **Audio layers** | Add music, voiceover, or sound effects. Set the start offset, trim, and volume of each layer |
| **Playback** | Play, loop, seek, zoom the timeline, and fit it to the window |
| **Selection** | Select several clips to download them or clear the selection |
| **Details** | Model, endpoint, status, request ID, length, start time, and trim for the selected item |
| **Prompt box** | Type a change, such as "swap shot 2 for a wider take", and the agent applies it |

Your edits are saved as you make them. The sequence card updates in place, so the agent and every open window see the same arrangement.

## Agent and user edits

The agent and you edit the same sequence. The agent can create the sequence, replace the clip list, and add or remove audio layers. You can do everything in the editor by hand. When the agent continues, it reads the live arrangement first, including your manual changes.

## Versions

A change by the agent creates a new version of the card at the bottom of the chat. The old card collapses to a receipt with **Jump to the latest sequence version**.

## Export

Click **Export as a video file** on the card or in the editor. The agent reads the live arrangement, with the clip order, trim windows, layer offsets, and gains, and renders exactly that in the [sandbox](sandbox.md). The rendered MP4 lands in the chat as a normal generation, and the editor closes.

A sequence is an arrangement, not a file. It does not take an ordinal and is not included in a [ZIP export](../generations/export.md). Export the sequence to get a file.
