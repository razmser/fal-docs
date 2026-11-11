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

# Retention and data

> How long fal Agent keeps prompts, generations, and chats, how organizations set a retention policy, and how external content is handled.

## Default retention

Self-serve accounts keep chats, prompts, and generations forever. You can delete a chat at any time from History, and you can remove a tile from a chat in the media rail.

## Organization retention policy

Enterprise accounts and teams under an organization can set a **Chat retention** period in **Settings → General**. The policy applies to the whole workspace.

| Options |
| :- |
| Forever, 1 hour, 2 hours, 6 hours, 12 hours, 1 day, 2 days, 3 days, 5 days, 1 week, 10 days, 2 weeks, 30 days, 90 days, 6 months, 1 year |

A team inherits the organization's policy unless it sets its own. The setting shows **Inherited from your organization** with a **Reset to org default** link when a team has overridden it. Only admins can change the policy.

### What the policy does

When a finite policy applies:

* Every upload and every model output is stamped with a matching lifetime on the fal CDN. The files expire.
* Every chat turn gets a matching expiry. Expired turns are hidden immediately and removed afterward.
* Request and response payloads for model runs are not archived.
* [Project memory](projects/memory.md) distillation is turned off, because notes have no expiry.
* Project documents cannot be uploaded, because an indexed document has no expiry.

The policy applies to new turns from the moment it is set. It does not shorten the lifetime of existing files.

## What the agent shares with models

Model runs send your prompt and your attached or referenced media to the fal model endpoint, as with any fal API call. The agent's reasoning runs on a language model operated for fal. Chat content is not used to train models.

## External content

Content the agent fetches from the web, from a downloaded video, or from a connected app is treated as data. Instructions inside that content do not change the agent's behavior. The same applies to text inside uploaded documents.

## Sharing

A shared chat is visible to the audience you choose until the share expires or you revoke it. See [Sharing and forking](chats/sharing.md).

## Delete data

* **Delete a chat** from History. This removes the chat and its turns.
* **Remove a tile** from the media rail. This removes it from the chat. The file stays in your Assets library until you delete it there.
* **Delete an asset from the library** at [fal.ai/assets](https://fal.ai/assets). This removes the library item, not the underlying CDN file. See [Assets retention and deletion](../assets/retention.md) for file-deletion controls.
