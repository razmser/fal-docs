> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Assets and characters

> Reuse library media in chats, organize collections, and reference characters and smart entities.

fal Agent uses [fal Assets](../assets.md), your media library on fal. Open the library at [fal.ai/assets](https://fal.ai/assets). Assets access follows your account's enablement and organization policy.

This page covers using Assets in a chat. For the full library guide, start with [Introduction to fal Assets](../assets.md).

## Automatic registration

Supported media attachments are registered as library uploads, and generated media is linked to its chat. The Assets browser displays images, video, audio, and 3D. Agent documents have their own [project workflow](projects/memory.md).

Open Assets, or use the `@` picker's **Assets** tab, to find available media from earlier work. Media remains subject to its retention policy.

## Reuse in a chat

* Type `@` and open the **Assets** tab to attach any library item.
* Open **Generations** in the palette to refer to an output from this chat.
* Drag a tile from the media rail into the composer, or use **Attach to chat** on the tile.

## Search

The agent can search your library on your behalf. It searches by text, by image similarity, and by video similarity. Ask for what you remember:

```text theme={null}
Find the warehouse shots from last week and use the widest one as the background.
```

## Collections

A collection is a named group of assets. The agent can create collections, add assets to them, and list what they hold. Add a collection to a project to make its contents part of the project context.

## Characters and smart entities

Assets supports **characters, props, environments, styles, and scenes**. Each smart entity has one to twenty reference images and an `@handle`. Characters also require a description. Select an entity from the `@` picker to give the agent reusable visual references for a generation.

References help guide the result. Consistency depends on the selected model and its inputs. See [Characters and smart entities](../assets/entities.md) for the entity types and [Using entities in generations](../assets/entities/using.md) for model-specific behavior.

```text theme={null}
Put @mara in a rain-soaked alley at night, then animate a slow dolly-in.
```

The agent can create a character for you from images in the chat, or you can create one in Assets. Character cards in the chat show the portrait, the handle, the bio, and the collections the character appears in.

The **character-design** [skill](skills.md) adds reference sheets, expression sheets, and outfit variations.

## Media lifetime

Files follow the lifetime applied to them. See [Agent retention](retention.md) for chat policies and [Assets retention and deletion](../assets/retention.md) for the difference between removing library items and deleting media files.
