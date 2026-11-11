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

# Collections and folders

> Group assets into manual collections, organize nested folders, and move media without duplicating it.

A manual collection is a named group of assets you choose. Use one for a campaign, delivery, moodboard, or set of references.

## Create a collection

Create a collection from the library's collection controls and give it a name. You can add a description to explain its purpose.

Open the collection and choose **Add assets**, or select media elsewhere in the library and add it through the collection picker. Uploading media directly into the collection also adds it there.

An asset can belong to more than one collection. Adding it to another collection does not create a new copy of the file or remove it from All media.

## Organize folders

Manual collections can contain subcollections, up to **five levels deep**. Use the collection tree or move controls to organize them. A collection cannot be moved inside itself or one of its descendants.

Opening a folder shows its own media and its subcollections. Open a subcollection to browse that subcollection's media. The parent view does not combine all descendant media into one grid.

Smart collections and smart entities cannot be nested in this folder tree.

## Move or remove media

* **Add to a collection** creates another membership.
* **Move from the current collection** changes the selected membership to the destination.
* **Remove from a collection** leaves the asset in the library and in any other collections it belongs to.

## Delete a collection

Deleting a collection does not delete its media. If it contains subcollections, those move to the deleted collection's parent, or to the top level if there is no parent.

To remove media from the library itself, use the asset's delete action. See [Retention and deletion](../retention.md).

## Related workflows

* Use [smart collections](smart-collections.md) when you want membership to follow rules.
* Use [tags](tags-and-favorites.md) for labels that span collections.
* Use [sharing](../sharing.md) to give recipients a viewing link to an eligible manual collection.
