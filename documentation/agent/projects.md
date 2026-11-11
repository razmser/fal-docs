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

# Projects

> Group chats, media, documents, characters, and collections under one shared memory.

A project is a container for related work. It holds chats, generated and uploaded media, documents, characters, collections, and a shared memory. The agent uses everything in the project as context, so you start a new session, not a new project.

## Create a project

Open **Projects** in the sidebar and click **New project**. Give it a name, such as "Horror Shorts". You can also start a project from a chat with **New project from this chat** in the chat header.

The Projects page lists your projects with search and sorting by **Last updated**, **Created at**, or **Name**. Rename or delete a project from its menu.

## The project home

Open a project to see its outputs grid and a side panel with its chats, characters, collections, and documents. The header offers:

| Action | Effect |
| :- | :- |
| **New chat** | Starts a chat inside the project |
| **Add resource** | Add media from Assets, upload media, upload documents, add characters, or add collections |
| **Add chats** | Move existing chats into the project |

## Add a chat to a project

From the breadcrumb in a chat header, choose **Add to a project** or **Move to another project**. A chat belongs to one project at a time. Moving it moves its media into the project's pool.

## What the project adds to a chat

* **Project scope in the media rail.** Switch the rail to **Project** to see every input and output across the project's chats.
* **Context tab.** The rail's **Context** tab lists memory notes and documents.
* **Memory tools.** The agent can search the project's memory and assets, read its documents, and save notes. See [Memory and documents](projects/memory.md).
* **Drop target.** Files dropped on the rail go to the project. See [Drag and drop](chats/composer.md#drag-and-drop).

## Private chats

A chat in a project contributes to the shared memory by default. To exclude one, choose **Private in project** from the breadcrumb. The chat stays in the project, but its turns no longer feed the memory.

> [card] [Memory and documents](projects/memory.md)
>
> How project memory is built, what the agent remembers, and how documents are indexed
