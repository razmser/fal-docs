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
> - [Deploy to Production](deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Rollbacks & Revisions

> Manage app revisions, roll back to previous versions, and restart runners.

Every time you run `fal deploy`, a new **revision** is created. Your app's alias (e.g., `my-model`) always points to one active revision. You can switch between revisions to roll back to a previous version instantly.

## Viewing Revisions

### CLI

```bash theme={null}
fal apps list-rev my-model
```

This shows all revisions with their IDs, creation dates, machine types, and scaling configuration.

### Dashboard

Navigate to [**Dashboard > Apps > \[your-app\] > Versions**](https://fal.ai/dashboard/apps). The versions page shows:

* All revisions with creation timestamps
* Status badges: **Deployed**, **Failed**, **Deploying**, **Suspended**
* The current active revision marked with a **Current** badge
* The message and annotations attached at deploy time (see [Annotating Deployments](deploy-to-production.md#annotating-deployments))

You can search revisions by message or annotation using the search box above the table. Click a revision to open its detail page with the full message and all annotations.

> [!TIP]
>
> Attach a message to every deploy so you can tell revisions apart at a glance when deciding what to roll back to.

***

## Rollouts

A rollout **replaces all runners** without changing the revision. Your app stays on the same code, but every runner is recycled onto the app's current configuration.

### CLI

```bash theme={null}
# Graceful rollout (replacements start before the old runners drain)
fal apps rollout my-model

# Force rollout (runners are killed immediately)
fal apps rollout my-model --force
```

### When to Use Rollouts

* **After changing secrets** -- Runners need to restart to pick up new environment variable values
* **Clearing bad state** -- A runner is stuck or has corrupted in-memory state
* **Memory cleanup** -- Long-running runners have accumulated memory that won't be freed
* **After a config change** -- You changed scaling params via CLI and want runners to restart cleanly
* **After a machine-type change** -- Existing runners keep the old machine type until they are replaced; see [Changing Machine Types](machine-types.md#rolling-out-existing-runners)

> [!WARNING]
>
> A force rollout (`--force`) kills runners immediately, dropping any in-progress requests. Use graceful rollouts in production unless you need an emergency restart.

***

## Rolling Back

Switch your app to a previous revision. The old code starts serving requests immediately -- no rebuild needed.

### CLI

```bash theme={null}
# List revisions to find the ID you want
fal apps list-rev my-model

# Switch to a specific revision
fal apps set-rev my-model <revision_id>
```

### Dashboard

1. Go to **Dashboard > Apps > \[your-app] > Versions**
2. Find the revision you want to roll back to
3. Click **Rollback**
4. Confirm in the dialog

> [!NOTE]
>
> When you roll back, runtime-tunable scaling parameters (`keep_alive`, `min_concurrency`, etc.) are inherited from the currently active revision. Code-specific parameters (`max_multiplexing`, `startup_timeout`, `machine_type`) come from the target revision's code.

### Rollback Strategies

When you roll back with `fal apps set-rev`, you can also choose how fal rolls traffic to the target revision:

```bash theme={null}
# Switch only after the target revision has a healthy runner
fal apps set-rev my-model <revision_id> --strategy rolling

# Switch immediately and let runners start on demand
fal apps set-rev my-model <revision_id> --strategy recreate
```

* **Rolling** starts a runner on the target revision first and waits for it to complete `setup()` and become healthy before switching the alias. Use this when you want the safest rollback with the least user-visible interruption.
* **Recreate** switches the alias to the target revision immediately. No runner is started proactively, so the next request may cold start the rolled-back revision. Use this when speed matters more than avoiding a brief interruption.

> [!NOTE]
>
> These strategies control how the rollback is applied. A rollback still switches your app to a different revision; a rollout still replaces runners without changing revisions.

### When to Roll Back

* A new deploy introduced a bug
* Model performance regressed after an update
* You need to quickly revert while investigating an issue

***

## Deleting Revisions

Clean up old revisions you no longer need.

### CLI

```bash theme={null}
fal apps delete-rev my-model <revision_id>
```

### Dashboard

On the **Versions** page, use the delete option on any non-current revision.

> [!NOTE]
>
> You cannot delete the currently active revision. Switch to a different revision first if you need to delete it.

***

## Rollback vs Rollout

| | Rollback | Rollout |
| - | - | - |
| **What changes** | Switches to a different revision (different code) | Replaces runners on the same revision |
| **Use case** | Revert to a previous version | Refresh runner state |
| **Rebuild needed** | No (previous image already exists) | No |
| **Downtime** | Brief (new runners start on the old revision) | None with a graceful rollout; `--force` drops in-flight requests |
| **CLI** | `fal apps set-rev` | `fal apps rollout` |
