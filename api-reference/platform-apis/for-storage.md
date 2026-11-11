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

# Platform APIs for Storage

> Manage access control, signed URLs, and lifecycle settings for fal CDN files

The **fal Platform APIs** for Storage provide programmatic control over files stored on the fal CDN, including:

* **File ACLs** - Read and replace the Access Control List of a CDN file to make it public, restrict it, or grant access to specific users
* **Signed URLs** - Mint time-limited URLs that grant temporary access to access-restricted files (valid up to 7 days)
* **Account storage settings** - Configure account-wide defaults for newly uploaded files, including auto-expiration and the initial ACL

## Available Operations

The Platform APIs provide the following endpoints for Storage:

> [card] [Get File ACL](../../platform-apis/v1/storage/files/acl/get.md)
>
> Retrieve the Access Control List currently applied to a fal CDN file

> [card] [Set File ACL](../../platform-apis/v1/storage/files/acl/set.md)
>
> Replace the Access Control List of a fal CDN file with a default decision and
> optional per-user rules

> [card] [Sign File URL](../../platform-apis/v1/storage/files/sign.md)
>
> Create a signed URL that grants temporary access to a file regardless of its
> ACL

> [card] [Get Storage Settings](../../platform-apis/v1/storage/settings/get.md)
>
> Read the account-level lifecycle settings applied to newly uploaded files

> [card] [Update Storage Settings](../../platform-apis/v1/storage/settings/update.md)
>
> Replace the account-level defaults for auto-expiration and the initial ACL of
> new uploads

## Access Control Lists

A file ACL is composed of a **default decision** plus optional **per-user rules** that override the default:

* `allow` - access is granted
* `forbid` - access is denied (the file returns an error)
* `hide` - access is denied and the file is treated as if it does not exist

Setting `default` to `allow` with no rules makes a file public. Using `forbid` or
`hide` restricts the file to only the users named in the rules.

> [!NOTE]
>
> Rules that reference users who do not exist are dropped. The response always
> reflects the ACL actually applied, so verify it contains the rules you sent.

## Authentication

All Storage endpoints require authentication. Include your API key in the `Authorization` header:

```
Authorization: Key YOUR_API_KEY
```

Permissions are split by capability:

| Operation | Required permission |
| - | - |
| Get file ACL / Sign file URL | `assets:read` |
| Set file ACL | `assets:write` |
| Get storage settings | `account:settings:read` |
| Update storage settings | `account:settings:write` |
