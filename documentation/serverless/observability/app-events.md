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
> - [Pricing](../pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# App Events

> Track deployments, runner lifecycle, and config changes with a full audit trail.

App Events gives you a chronological timeline of everything that happens to your application -- who deployed, when runners started or failed, and what configuration changed. Use it to debug incidents, audit team activity, and understand your app's lifecycle.

## What You Can Track

Navigate to [**Dashboard > Apps > \[your-app\] > Events**](https://fal.ai/dashboard/apps) to see the timeline. You can search, filter by event category (runner, deployment, config), and set a date range.

App Events tracks three categories of activity:

**Runner events** show the lifecycle of individual runners: when they were scheduled, when they started pulling your image, when `setup()` ran, and when they became ready or failed. Failure events include the stage that failed (SETUP, DOCKER\_PULL, etc.), so you can quickly tell whether the problem is your image, your setup code, or infrastructure.

**Deployment events** track when deployments start, succeed, or fail. Use these to correlate performance changes with deploys. If latency spiked right after a deploy, you know where to look.

**Config change events** record who changed what, with a diff of old vs new values. This is the audit trail for scaling parameter changes, machine type updates, and auth mode switches.

## API Access

Query events programmatically:

```bash theme={null}
curl "https://rest.fal.ai/applications/your-username/your-app/events?since=2026-02-17T00:00:00Z" \
  -H "Authorization: Key $FAL_KEY"
```

### Parameters

| Parameter | Description |
| - | - |
| `since` | Start time (ISO 8601) |
| `until` | End time (ISO 8601) |
| `categories` | Filter by event category. Repeat the parameter to pass several. Runner values are `runner_started`, `runner_pending`, `runner_docker_pull`, `runner_setup`, `runner_finished`, `runner_failed`, `runner_startup_failure`, `runner_draining`, `runner_stopping`; deployment values are `deployment_started`, `deployment_ended`, `deployment_failed`, `deployment_rolling_started`, `deployment_rolling_ended`, `deployment_rolling_failed`, `deployment_rolling_rolled_back`, `deployment_recreate_applied`; config changes are `config_changed`. |

> [card] [Error Analytics](error-analytics.md)
>
> Analyze error patterns across your apps

> [card] [Slack Notifications](slack-notifications.md)
>
> Get notified when runners fail
