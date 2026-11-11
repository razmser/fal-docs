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

# Retention and deletion

> Understand the difference between organizing media, removing library assets, and deleting or expiring files.

An asset's place in your library, its underlying media file, and its generation's request data have different lifetimes. Choose the operation that matches what you want to remove.

## Library actions

| Action | What happens |
| :- | :- |
| Remove from a collection | Removes that membership. The asset remains in the library and any other collections. |
| Delete a manual collection | Removes the collection, not its media. Subcollections move to its parent or to the top level. |
| Delete a smart collection | Removes its saved rules, not the matching media. |
| Delete a smart entity | Removes the reusable entity, not its reference files or outputs. |
| Delete an asset | Removes the asset from the library and search. This action does not itself delete the underlying CDN file. |

The library does not offer a Trash or restore workflow. Check your selection before deleting assets.

## File lifetime

Media files follow the storage lifetime applied to them. Enabling Assets, placing a file in a collection, or sharing a collection does not itself extend a file's lifetime.

If a file expires, it can no longer be downloaded or reused as a reference. A saved entity or recorded generation relationship does not preserve an expired file. Download a copy before expiry when you need to keep the media outside fal.

See [Data retention and storage](../model-apis/media-expiration.md) for CDN lifecycle controls and request-payload retention. For work created in Agent, also see [Agent retention](../agent/retention.md).

## Delete generation files and request data

To delete a completed generation's stored request payloads and associated CDN output files, use [Delete request payloads](../../platform-apis/v1/models/requests/payloads.md). Input files are not deleted by that operation because they can be used by other requests.

Do not use the library's delete action as a substitute for deleting generation files or revoking access to a URL. See [File access controls](../model-apis/file-access-controls.md) to manage file access.

## Shares and downloaded copies

Stopping a collection share revokes that viewing link. It does not remove files already downloaded by recipients. A share's expiry is separate from file expiry.
