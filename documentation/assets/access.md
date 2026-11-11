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

# Access and availability

> Enable fal Assets, choose the right account, and understand team permissions and enterprise access.

## Enable Assets

Sign in at [fal.ai/assets](https://fal.ai/assets). If Assets is available but not yet enabled, review the terms. Select the agreement checkbox. Choose **Enable fal Assets**.

Enabling Assets allows your generations to be saved and organized, reused on supported fal surfaces, and processed for semantic search. A role with read-only access cannot enable or modify the library.

## Personal and team libraries

Assets uses the currently selected account. Switch accounts to move between your personal library and a team's library. A file in one account does not automatically appear in another account's library.

If expected media is missing, check the account before uploading it again.

## Permissions

Your role determines whether you can browse the library, upload media, edit entities and collections, or delete assets. Request-payload permissions also affect whether you can inspect prompts and generation details.

If you can view an asset but cannot edit it, ask an account administrator to check your role. See [Teams](../setting-up/teams.md) for account membership.

## Organization and enterprise access

An organization can disable Assets for its teams. If the page says Assets is disabled for the organization, an organization administrator must enable it.

Enterprise accounts that have not enabled Assets must contact [support@fal.ai](mailto:support@fal.ai) to discuss access. Enabling Assets on another account does not override an organization's policy.

## Which generations appear

The request sources included in Assets depend on your account's configuration. Access to the library does not by itself mean every API request is included. If browser generations appear but API generations do not, check the account's configuration with your administrator or support.

## Features with separate requirements

* **Agent reuse** requires [fal Agent access](../agent/access-and-pricing.md).
* **New model generations** require the model's normal access and credits.
* **Collection sharing** is available only for eligible independent self-service personal accounts. See [Sharing collections](sharing.md).
* **Programmatic access** requires an API key for an account with Assets enabled. See [Using the Assets API](api.md).
