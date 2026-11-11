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

# Aggregate Analytics

> Get a cross-app overview of capacity, traffic, and health across every serverless app you own.

While [App Analytics](app-analytics.md) drills into a single app, the aggregate analytics dashboard answers the higher-level question: how is *everything* you have deployed doing right now? It opens automatically at the top of the [Apps page](https://fal.ai/dashboard/apps) and summarizes capacity, traffic, and per-app activity across all of your apps in one place. This is especially useful once you are running tens of apps -- whether they are stages of one pipeline or independent endpoints -- and need to spot problems without clicking into each one.

## What the Overview Shows

The dashboard at the top of the Apps page combines several components that aggregate across every app you own:

* **GPU and CPU distribution** -- how your active runners are split across machine types (H100, H200, A100, L40, B200, RTX 6000, and CPU classes), so you can see where your capacity is concentrated.
* **Top apps** -- your highest-volume apps over the selected window (for example, the last 6 hours) by total requests.
* **Requests by status code** -- aggregate request traffic broken down by success, user errors, and server errors.
* **Request traffic** -- requests per second across all apps combined.
* **Requests by app** -- a stacked chart showing which apps are contributing the most requests to your combined usage.

## Organizing and Scanning Many Apps

Alongside the charts, the Apps page gives you tools to manage a large fleet of apps at a glance:

* **List view** -- a compact row-based layout that makes it practical to work with tens of apps. Each row surfaces key metrics (runners, error rate, latency) plus a per-app request breakdown by status code over time, so you can quickly scan for apps with a high runner count or an elevated error rate.
* **Tags** -- label apps by modality, team, project, or any scheme that fits your workflow (for example, tag an upscaler app as `upscaler` or a streaming app as `real-time`), each with its own color.
* **Filtering** -- filter the listing by tag to instantly narrow down to a group of apps, then drill into any specific app that needs a closer look.

## From Overview to Root Cause

The aggregate view is the starting point of the observability workflow. Use it to notice something worth investigating -- an app with a spike in errors, or a machine type consuming more capacity than expected -- then drill into that app's [App Analytics](app-analytics.md), [Requests](monitor-performance.md), [Runner Analytics](runner-analytics.md), or [Logs](logs.md) to find the root cause.

> [card] [App Analytics](app-analytics.md)
>
> Per-app request metrics, latency percentiles, and cold boot rate

> [card] [Runner Analytics](runner-analytics.md)
>
> Cold start stages, telemetry, and per-runner debugging

> [card] [Error Analytics](error-analytics.md)
>
> Error rates, patterns, and failure breakdown

> [card] [Manage Deployments](../../deployment/manage-deployments.md)
>
> List apps, manage versions, and organize with tags
