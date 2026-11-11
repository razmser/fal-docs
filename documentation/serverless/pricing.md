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
> - [Pricing](pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Serverless GPU Pricing: Per-Second Billing

> fal Serverless bills runners per second by machine type while they are alive, including setup and idle time. Scheduling and image pulls are not billed.

When you deploy your own applications on fal Serverless, you are billed for the **total time your runners are alive**, measured per-second by machine type.

## Billing by runner state

Every runner transitions through the states below during its lifecycle. You are billed for the states marked **Yes** at the per-second rate for your machine type.

| State | Billed | Description |
| - | - | - |
| **PENDING** | No | Waiting to be scheduled on available hardware |
| **DOCKER\_PULL** | No | Pulling your container image from the registry |
| **SETUP** | Yes | Running your `setup()` method — loading models, initializing resources |
| **IDLE** | Yes | Runner is ready but waiting for requests (includes `keep_alive` time) |
| **RUNNING** | Yes | Actively processing one or more requests |
| **DRAINING** | Yes | Finishing in-flight requests before shutdown |
| **TERMINATING** | Yes | Running your `teardown()` method |
| **TERMINATED** | No | Runner has stopped and resources are released |

5xx errors (HTTP 500+) are also not charged.

See [Runners](../deployment/runners.md) for full details on each state and transitions.

## GPU count multiplier

Multi-GPU instances are billed as `gpu_count x duration`. For example, a runner using 2x A100 GPUs for 60 seconds is billed as 120 GPU-seconds.

## Monitoring your usage

> [card] [Dashboard Billing](https://fal.ai/dashboard/billing)
>
> View your overall spend, invoices, and payment methods.

> [card] [App Analytics](observability/app-analytics.md)
>
> See per-app cost breakdown, request counts, and runner utilization.
