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

# Sharing collections

> Share a manual collection with a viewing link, choose its audience, and manage expiry or revocation.

Collection sharing gives recipients a way to browse and download selected work without editing your library.

> [!NOTE]
>
> Creating collection shares is available only for eligible independent self-service personal accounts. Team accounts, organization accounts, and enterprise accounts cannot create collection shares through this feature. A shared collection must be a manual collection. Smart collections and entities are not shareable collections.

## Create a link

Open a manual collection's share action. Choose who can access it, choose a link lifetime, and create the link. Copy the link for your recipients.

The dialog offers **Everyone with link** and **Only specific people**. Restricted recipients must sign in with an identity allowed by the share. Public-link recipients can view without enabling their own Assets library.

Link lifetime options are **Never**, **1 day**, **7 days**, and **30 days**. “Never” means the share has no scheduled expiry. It does not extend the lifetime of the underlying files.

## What recipients see

Recipients get a viewing surface for the shared collection. They can preview supported media and download available files, including a selection as a ZIP. They cannot edit, delete, favorite, or reorganize your source collection.

The shared media view does not expose generation prompts or request details. Review the media itself before sharing: visible content in an image or video is still visible to recipients.

The view follows the shared collection's available contents. It is not an immutable snapshot or a copy of the files.

## Update or stop sharing

Reopen the share dialog to update the audience or expiry. Use **Stop sharing** to revoke the link. Expired, revoked, deleted, or otherwise unavailable shares can no longer be opened through that link.

Revoking a share does not recall copies recipients have already downloaded. Removing a library item or changing share access is also distinct from deleting or restricting a CDN file. See [Retention and deletion](retention.md) and [File access controls](../model-apis/file-access-controls.md).
