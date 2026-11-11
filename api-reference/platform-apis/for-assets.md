> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../documentation/quickstart.md) and the [Model APIs overview](../../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Platform APIs for Assets

> Programmatic access to fal Assets for browsing, searching, uploading, and organizing generated media

The **fal Platform APIs** provide programmatic access to fal Assets, including:

* **Browse and search** - List assets, filter by media type or source, and search with text or fal-hosted media
* **Uploads** - Add media to your Assets library from API or CLI-driven workflows
* **Collections** - Create manual collections and add or remove assets
* **Characters** - Create reusable character collections with reference images
* **Tags and favorites** - Organize assets with tags and favorite state

Use these APIs when you want generated outputs, uploaded references, and reusable creative assets to be available outside the dashboard. For generating new media, use the [Model APIs](../../documentation/model-apis/overview.md), then use Assets to browse, search, and organize the media that belongs in your library.

For an end-to-end example, see [Using the Assets API](../../documentation/assets/api.md). For browser workflows, see the [fal Assets guide](../../documentation/assets.md).

## Available Operations

The Platform APIs provide the following endpoints for fal Assets:

> [card] [Browse Assets](../../platform-apis/v1/assets.md)
>
> Browse and semantically search assets across media, uploads, favorites, collections, tags, and character references

> [card] [Upload Asset](../../platform-apis/v1/assets/uploads.md)
>
> Upload fal-hosted media into your Assets library

> [card] [Get Asset](../../platform-apis/v1/assets/get.md)
>
> Retrieve details for an asset in your library

> [card] [Favorite Asset](../../platform-apis/v1/assets/favorite.md)
>
> Favorite an asset by vector ID or request ID

## Collections

> [card] [List Collections](../../platform-apis/v1/assets/collections.md)
>
> List asset collections in your library

> [card] [Create Collection](../../platform-apis/v1/assets/collections/create.md)
>
> Create a collection for organizing related assets

> [card] [Browse Collection Assets](../../platform-apis/v1/assets/collections/assets.md)
>
> Browse assets inside a collection

> [card] [Add Asset to Collection](../../platform-apis/v1/assets/collections/assets/add.md)
>
> Add an asset to a collection by vector ID or request ID

## Characters

> [card] [List Characters](../../platform-apis/v1/assets/characters.md)
>
> List character collections in your library

> [card] [Create Character](../../platform-apis/v1/assets/characters/create.md)
>
> Create a reusable character from descriptions and reference images

> [card] [Get Character](../../platform-apis/v1/assets/characters/get.md)
>
> Retrieve a character collection

> [card] [Favorite Character](../../platform-apis/v1/assets/characters/favorite.md)
>
> Mark a character collection as a favorite

## Tags

> [card] [List Tags](../../platform-apis/v1/assets/tags.md)
>
> List tags in your Assets library

> [card] [Create Tag](../../platform-apis/v1/assets/tags/create.md)
>
> Create a tag for organizing assets

> [card] [Set Tags for Asset](../../platform-apis/v1/assets/tags/set-for-asset.md)
>
> Replace the full tag set for an asset

> [card] [Assign Tag](../../platform-apis/v1/assets/tags/assign.md)
>
> Assign a tag to an asset by vector ID or request ID

## Authentication

Assets endpoints require an API key in the `Authorization` header:

```bash theme={null}
Authorization: Key YOUR_API_KEY
```

> [!NOTE]
>
> Assets must be enabled for the account associated with your API key. If generated media is missing, check the selected account and which request sources its configuration includes. See [Access and availability](../../documentation/assets/access.md).
