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

# Observability Overview

> Monitor your fal applications using the dashboard, CLI, and programmatic integrations.

Once your app is deployed and serving traffic, you need visibility into how it is performing. fal provides three interfaces for observability, each suited to different workflows. The [dashboard](https://fal.ai/dashboard) gives you visual, real-time insight into requests, runners, and errors. The [CLI](../../../api-reference/cli/runners.md) lets you check runner states and stream logs from your terminal. And the programmatic integrations (Prometheus metrics, log drains, Slack notifications) let you pipe fal's data into your existing monitoring stack.

Understanding which interface to use for which task saves you from digging through the wrong tool. The dashboard is best for investigating specific issues and getting a quick overview. The CLI is best for quick operational checks and scripting. The integrations are best for production alerting, long-term metrics storage, and centralized logging.

> [!TIP]
>
> Prefer to debug from your editor? Connect the [Platform MCP](../../setting-up/platform-mcp.md) and let your AI assistant walk requests, logs, analytics, and runner state for you — see [Debugging with AI](debug-with-ai.md) for the full workflow.

## Dashboard

The [fal dashboard](https://fal.ai/dashboard) is the primary interface for visual monitoring. It provides several views depending on what you need to understand.

**App Analytics** shows request volume, latency, runner counts, and queue depth for each deployed app. Use it to understand traffic patterns, identify slow endpoints, and verify that scaling is working correctly. The Requests tab shows individual request history with status, duration, and logs. The Runners tab shows runner lifecycle events and state transitions.

**Error Analytics** breaks down failures by error type, status code, and time. Use it to spot error spikes, identify recurring failure patterns, and understand which errors are transient vs systemic.

**App Events** tracks deployment events, configuration changes, and runner lifecycle events. Use it to correlate performance changes with deployments (e.g., "latency increased right after this deploy").

**Logs** streams runner logs in real-time and lets you filter by runner ID, request ID, version, and source. For understanding how request-level logs work, see [Logging](../../development/logging.md).

> [card] [App Analytics](app-analytics.md)
>
> Request metrics, runner states, and per-app performance

> [card] [Error Analytics](error-analytics.md)
>
> Error rates, patterns, and failure breakdown

> [card] [App Events](app-events.md)
>
> Deployment, config, and runner lifecycle events

The Requests page lets you go from an aggregate timeline of all traffic to a single request -- drag to filter a spike, sort by startup or duration, and drill into error types:

## CLI

The fal CLI provides quick access to runner states, logs, and queue status from your terminal. This is useful for operational checks, scripting, and CI pipelines.

```bash theme={null}
# Runner monitoring
fal runners list                        # all runners with current states
fal runners list --state idle           # filter by state
fal runners list --since "1h"           # view history
fal runners logs <runner-id>            # stream logs from a runner

# Queue monitoring
fal queue size my-app                   # current queue depth
fal queue flush my-app                  # clear stuck requests

# App management
fal apps list                           # all deployed apps
fal apps runners my-app                 # runners for a specific app
fal apps scale my-app                   # view/update scaling config
```

See the CLI reference for [`fal runners`](../../../api-reference/cli/runners.md), [`fal queue`](../../../api-reference/cli/queue.md), and [`fal apps`](../../../api-reference/cli/apps/list.md).

## Programmatic Integrations

For production monitoring, you can pipe fal's data into your existing observability stack. This gives you long-term retention, custom dashboards, and alerting that the built-in dashboard does not provide.

**Prometheus Metrics** -- fal exposes a Prometheus-compatible metrics endpoint that you can scrape with Grafana, Datadog, or any Prometheus-compatible tool. Available metrics include runner counts, queue depth, concurrent requests, request throughput, and latency buckets.

**Log Drains** -- forward runner logs to Datadog through the built-in integration or to any HTTPS webhook as NDJSON. Logs include request IDs, timestamps, and structured metadata for correlation.

**Slack Notifications** -- receive alerts in Slack when runners fail to start, deployments complete, or configuration changes occur.

> [card] [Exporting Metrics](exporting-metrics.md)
>
> Prometheus endpoint, Grafana setup, and Datadog integration

> [card] [Log Drains](log-drains.md)
>
> Forward logs to external services in real-time

> [card] [Slack Notifications](slack-notifications.md)
>
> Alert on deployments, failures, and config changes
