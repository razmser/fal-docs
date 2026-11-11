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

# Tags and favorites

> Label assets across collections and keep frequently used media easy to find.

## Tags

Tags are labels you apply to assets. Use them for concepts that cut across collections, such as `approved`, `needs-review`, or `summer`.

Open an asset's **Tags** section to add an existing tag or create one. To label several assets, select them in the library and use the tag control in the selection toolbar.

An asset can have multiple tags. Removing a tag from an asset does not remove the media from the library or its collections.

## Filter by tags

Select tags in the library filters and choose how they must match:

* **Any** returns assets with at least one selected tag.
* **All** returns assets with every selected tag.

For example, select `summer` and `approved` with **All** to find approved summer assets. Combine tags with a description search, media type, or collection scope.

## Favorites

Favorite an asset from its available actions to make it easy to return to in **Favorites**. You can also favorite a selection of assets.

Collections and smart entities have their own favorite controls. Favoriting one marks that collection or entity. It does not favorite every asset associated with it.

Unfavoriting an item removes its favorite state. It does not delete it.

> [!NOTE]
>
> Favorites are an organization tool. Do not rely on a favorite as a backup or as a guarantee that a file outlives its retention policy. See [Retention and deletion](../retention.md).
