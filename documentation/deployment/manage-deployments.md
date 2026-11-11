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

# Manage Deployments

> Once your models are deployed to production, you need tools and strategies to manage them effectively. This guide covers listing deployments, monitoring application health, managing multiple versions, and safely removing models.

## Listing Deployments

View all your deployed models to understand your current production environment:

```bash theme={null}
fal apps list
```

This shows all your deployed models with their current status, versions, and basic configuration. Use this to get an overview of your model portfolio and identify which models need attention.

## Testing a Deployment

Open the listed deployment from the [Apps dashboard](https://fal.ai/dashboard/apps). Follow [Testing Your Own Apps](../model-apis/playground.md#your-own-apps) to test an endpoint. Temporary apps started by `fal run` use the Playground URL printed for that run.

## Organizing Apps with Tags

You can assign custom tags to your apps from the [fal dashboard](https://fal.ai/dashboard) to keep your app list organized. Tags are visual labels — use them to group apps by team, project, model type, or any category that fits your workflow, then filter the app listing to quickly find what you need.

> [!NOTE]
>
> Tags are managed entirely through the dashboard UI — they don't isolate secrets, affect routing, or change app behavior. If you need isolated deployment stages (dev, staging, production) with separate secrets and endpoints, use [Environments](https://fal.ai/docs/serverless/deployment-operations/manage-environments.md) instead.

## Managing Versions

### Viewing Model Revisions

Each deployment creates a new revision. List all revisions for a specific model:

```bash theme={null}
fal apps list-rev myapp
```

Revisions can carry a freeform message and custom annotations set at deploy time — see [Annotating Deployments](deploy-to-production.md#annotating-deployments). Unlike [tags](#organizing-apps-with-tags), which group apps for dashboard organization, message and annotations describe a single revision. They don't appear in `fal apps list-rev` output — view and search them on the app's **Versions** page in the dashboard or fetch them via the [revisions API](../../platform-apis/v1/serverless/apps/revisions.md). For example, to stamp each revision with the current commit:

```bash theme={null}
fal deploy --message "$(git rev-parse --short HEAD) fix cold-start"
```

### Switching Between Revisions

If you need to roll back to a previous model version or switch to a different revision:

```bash theme={null}
fal apps set-rev myapp uuid_of_revision
```

This is useful for quick rollbacks when a new model version has issues in production.

## Scaling Deployments

After deployment, you can adjust scaling parameters without redeploying:

```bash theme={null}
fal apps scale myapp --min-concurrency 2 --max-concurrency 20
```

For comprehensive scaling configuration and strategies, see [Scale Your Application](https://fal.ai/docs/serverless/deployment-operations/scale-your-application.md).

## Monitoring Health

### Basic Health Checks

Monitor your model's health and performance using the fal dashboard, or check active runners:

```bash theme={null}
fal apps runners myapp
```

> [!NOTE]
>
> For detailed performance monitoring, metrics collection, and alerting strategies, see [Monitor Performance](https://fal.ai/docs/serverless/deployment-operations/monitor-performance.md).

## Removing Deployments

### Safe Deletion Process

Before deleting a model deployment:

**1. Verify dependencies:**

Ensure no other services or applications depend on this model

**2. Check traffic:**

Review recent usage to understand impact

**3. Backup if needed:**

Document model configuration or export important data

### Delete a Deployment

```bash theme={null}
fal apps delete myapp
```

**Warning**: This permanently removes the model deployment. The model cannot be recovered after deletion.

### Delete Specific Revisions

To clean up old revisions while keeping the current deployment:

```bash theme={null}
fal apps delete-rev myapp --revision-id abc123
```
