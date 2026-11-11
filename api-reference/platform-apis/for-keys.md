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

# Platform APIs for Keys

> Programmatic access to API key management, creation, and revocation

The **fal Platform APIs** provide programmatic access to API key management, including:

* **List keys** - Retrieve the API keys in your account, with the permissions of each key
* **Create keys** - Generate new keys on a preset or with a custom permission list
* **Revoke keys** - Permanently disable existing API keys

## Available Operations

The Platform APIs provide the following endpoints for managing API keys:

> [card] [List API Keys](../../platform-apis/v1/keys/list.md)
>
> Retrieve all keys, with the mode, preset, and permissions of each key

> [card] [Create API Key](../../platform-apis/v1/keys/create.md)
>
> Generate a new key on a preset or with a custom permission list

> [card] [Revoke API Key](../../platform-apis/v1/keys/revoke.md)
>
> Permanently revoke a key and keep its audit record

To create a key, send a `preset` or a `permissions` list. If you send neither, the key gets the `API` preset. A new key cannot have permissions that the calling key does not have. To limit a key to specific models, use the [dashboard](https://fal.ai/dashboard/keys).

> [!NOTE]
>
> To list keys, use a key on the `BILLING` or `FULL` preset. To create or revoke a key, use a key on the `FULL` preset. A custom policy key with the same permissions also works. For more information, see [Key permissions](../../documentation/model-apis/authentication/key-based.md#permissions).
