> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](quickstart.md) and the [Model APIs overview](model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Introduction to fal Assets

> Find, organize, and reuse images, videos, audio, and 3D assets from your fal generations and uploads.

fal Assets is your media library on fal. It brings your generated media and uploaded references into one place. Search for earlier work, organize it into collections, and reuse it in your next generation.

Open your library at [fal.ai/assets](https://fal.ai/assets). The library belongs to your selected account, so personal work and team work have separate libraries.

## What fal Assets does

* **Finds media.** Search with a description, an image, or a video, then narrow the results with filters.
* **Organizes your work.** Group assets into collections and folders, apply tags, and favorite items you return to often.
* **Keeps reusable references.** Create characters, props, environments, styles, and scenes with reference images and an `@handle`.
* **Connects generations.** Inspect a generation's prompt and inputs, and see later generations that used an asset.
* **Makes media reusable.** Pick library assets in supported fal model inputs or reference them in [fal Agent](agent.md).
* **Exports and shares results.** Download files or a ZIP. Eligible accounts can share manual collections with a viewing link.

## From first upload to next generation

**1. Add**

Upload reference media or find an output from a generation included in your account's library.

**2. Find**

Browse recent media, describe what you remember, or search with an image or video example.

**3. Organize**

Add selected assets to a collection, tag them, or turn reference images into a smart entity.

**4. Reuse**

Select the asset in a supported model input, attach it in Agent, or download it for use elsewhere.

## Access

Assets must be enabled for your account. Enablement includes permission to process request payloads for semantic search. Your account type, organization policy, and role determine which features you can use. See [Access and availability](assets/access.md).

## Start here

> [card] [Quickstart](assets/quickstart.md)
>
> Upload, organize, find, and reuse your first asset

> [card] [Searching and filtering](assets/library/search.md)
>
> Find media by description or example

> [card] [Collections and folders](assets/organizing/collections.md)
>
> Organize a campaign or project

> [card] [Characters and smart entities](assets/entities.md)
>
> Reuse characters, props, environments, styles, and scenes

> [card] [Retention and deletion](assets/retention.md)
>
> Understand library removal and file lifetime

> [card] [Using the Assets API](assets/api.md)
>
> Browse and organize media programmatically
