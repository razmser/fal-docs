> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../quickstart.md) and the [Model APIs overview](../overview.md).
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

# Key-Based Authentication

> Authenticate fal API requests with a secret key from the dashboard. Choose a permission preset or a custom permission list, and limit a key to specific models.

Key-based authentication lets you authenticate fal API requests using a secret key generated from the dashboard. This is the recommended authentication method for production applications, CI/CD pipelines, and any environment where you need programmatic access.

Each key carries a permission set that controls the operations the key can do. You can create and manage keys from the fal dashboard at any time.

## Overview

There are two main reasons to use key-based authentication:

1. When calling [ready-to-use models](https://fal.ai/models)
2. In headless remote environments or CI/CD (where GitHub authentication is not available)

## Generating the keys

Navigate to our dashboard keys page and generate a key from the UI [fal.ai/dashboard/keys](https://fal.ai/dashboard/keys)

## Permissions

Permissions control what a key can do. When you create a key, you select a preset or a custom permission list. A permission name has parts that colons separate, for example `models:requests:submit`, `serverless:apps:write`, or `models:list`.

### Presets

A preset is a permission list that fal curates for a common case. If you only consume [ready-to-use models](https://fal.ai/models), use the `API` preset.

| Preset | What a key with this preset can do |
| - | - |
| `API` | Submit, read, cancel, and share model requests. Read the model catalog and model analytics, run workflows, upload or download files, and create short-lived auth tokens. |
| `SERVERLESS` | Everything in `API`, plus deploy, run, and delete your own apps. Read logs and queues, and manage secrets, environments, and log drains. |
| `DEPLOY` | Deploy from a CI/CD pipeline. Read, deploy, run, and delete apps. Upload files, read logs, and manage secrets and environments. |
| `COMPUTE` | Read, create, and delete compute instances. Read and terminate runners. |
| `BILLING` | Read usage, invoices, contracts, billing settings, the list of keys, and the model catalog. |
| `READONLY` | Read requests, apps, logs, runners, compute instances, workflows, and usage. This preset grants no write access. It does not read invoices, contracts, billing settings, keys, team members, secrets, environments, or log drains. |
| `FULL` | Every permission on the account, including private models and all CLI operations. |

> [!NOTE]
>
> If you are not sure which preset to select, start with `API`. You can create a
> second key with more permissions when you need one.

### Custom policies

If no preset matches your case, select the exact permissions the key needs. A custom policy is useful when you must grant one narrow capability, such as a key that only submits requests to your own app.

### Allowed models

You can limit a key to a list of models in the **Allowed models** tab when you create the key. You cannot change the limit after creation. A key with this limit can only call the models on the list.

> [!WARNING]
>
> If you add a model limit to a preset, fal stores the key as a custom policy. The key then keeps the preset permissions from the moment of creation, and it no longer follows the live preset.

### How a key resolves its permissions

A key resolves its permissions under one of two contracts:

* A **preset** key stores only the preset name. fal curates the presets and can refine a definition, and the key follows the live definition. A change to the preset applies to the key at its next request.
* A **custom policy** key stores the full permission list. The list is a snapshot from the moment of creation, and fal does not redefine it.

If the permissions of a key must stay the same for the life of the key, use a custom policy.

### Expiration

When you create a key, you can set it to expire after a period from 1 hour to 1 year, or never. The keys page shows an **Expiring soon** badge before a key expires, and an **Expired** badge after. An expired key cannot authenticate requests. To replace it, create a new key.

### Legacy scopes

Keys created before presets carry the legacy `ADMIN` or `API` scope, and they continue to work. An `ADMIN` key has full access to the account, and the closest preset is `FULL`. An `API` key can call models, and the closest preset is `API`. To move a legacy key to the preset model, create a new key with a preset and revoke the old key.
