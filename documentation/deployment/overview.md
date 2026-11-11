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

# Deployment Overview

> Ship your fal applications to production, manage revisions and environments, configure scaling, and choose the right machine type.

Deployment is where your [fal App](../development/app-lifecycle.md) goes from local development to a production API that serves real traffic. When you run `fal deploy`, fal builds your code into a container image, pushes it to a registry, and makes it available at a permanent endpoint ID. From that point, [runners](runners.md) spin up on demand to handle requests, scale automatically based on traffic, and shut down when idle. You can roll back to any previous version instantly, deploy to separate [environments](manage-environments.md) for staging and production, and tune [scaling parameters](scale-your-application.md) without redeploying.

Before diving into this section, make sure you have [installed the CLI](../development/getting-started/installation.md) and built your app following the [Development](../serverless.md) guides. The pages here cover everything after your code is written: understanding what runners are, deploying to production, managing versions and environments, choosing hardware, and configuring scaling. If you are migrating from another platform, the [migration guides](../development/migrate-external-docker-server.md) can help you get started faster.

## Quick Start

The simplest deployment is a single command:

```bash theme={null}
fal deploy path/to/myapp.py::MyApp
```

This builds your app remotely, creates a persistent deployment, and gives you an endpoint ID like `your-username/my-model` that callers use with the [fal client SDKs](../model-apis/inference.md).

## Runners and Requests

Before deploying, it helps to understand the execution model. When a caller submits a request, it enters a persistent queue and is dispatched to a runner. Runners are compute instances that pull your container image, run `setup()`, and serve requests until they scale down. Understanding how requests flow through the queue and how runners start, process, and shut down is essential for debugging latency, configuring scaling, and managing costs.

> [card] [Understanding Requests](requests.md)
>
> Request lifecycle, retry interaction, and platform architecture diagram

> [card] [Understanding Runners](runners.md)
>
> Runner lifecycle states, startup, shutdown, and scaling behavior

> [card] [Caching](caching.md)
>
> How Docker layers, model weights, and compiled artifacts are cached across runners

## Deploying and Managing

Deployment creates a versioned revision of your app. Each deploy creates a new revision, so you can roll back to any previous version instantly if something goes wrong. You choose a rollout strategy (recreate for speed, rolling for zero downtime), configure authentication (private, public, or shared billing), and optionally deploy to separate environments for staging and production.

> [card] [Deploy to Production](deploy-to-production.md)
>
> Build, configure, and ship your app with rollout strategies and auth modes

> [card] [Manage Deployments](manage-deployments.md)
>
> List, update, and delete deployed apps

> [card] [Rollbacks](rollbacks.md)
>
> Switch between revisions or replace runners with rollouts

> [card] [Environments](manage-environments.md)
>
> Isolate staging and production with separate secrets and config

## Scaling and Hardware

Once deployed, you control how your app scales. Scaling parameters determine how many runners stay warm, how quickly new ones spin up, and how many requests each runner handles concurrently. Machine types determine the hardware backing each runner, from lightweight CPU instances to H200 and B200 GPUs.

> [card] [Scaling Parameters](scale-your-application.md)
>
> Configure concurrency, keep-alive, multiplexing, and scaling delays

> [card] [Scaling Configuration](scaling-configuration.md)
>
> Set parameters via code, CLI, or dashboard, and understand --reset-scale

> [card] [Machine Types](machine-types.md)
>
> Choose from CPU and GPU hardware based on memory, compute, and VRAM needs
