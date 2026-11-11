> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](quickstart.md) and the [Model APIs overview](model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Introduction to Compute

> Dedicated GPU instances for training, fine-tuning, and workloads that need sustained access to hardware.

fal Compute gives you dedicated GPU instances that run continuously under your control. Unlike [Serverless](deployment/deploy-to-production.md), where runners scale automatically and you pay per-second of execution, Compute instances stay running for as long as you need them. You SSH in, install your stack, and use the GPU directly. This makes Compute the right choice for training runs, long-running fine-tuning jobs, batch processing, and any workload where you need sustained, predictable access to hardware rather than on-demand scaling.

Compute runs on NVIDIA H100 SXM GPUs with high-speed SSD storage. For distributed workloads, 8-GPU instances can be provisioned in the same sector, connecting them over InfiniBand for low-latency multi-node communication. Instances are billed per-hour at fixed rates, so your costs are predictable regardless of how you use the GPU. For workloads that are better served by autoscaling and pay-per-use, see [Serverless](serverless/pricing.md) instead.

## Instance Types

Two instance types are available, both built on H100 SXM GPUs:

| Instance Type | GPUs | vCPU | RAM | VRAM | Storage |
| :- | :- | :- | :- | :- | :- |
| **1xH100-SXM** | 1 | 16 | 200 GB | 80 GB | 1 TB SSD |
| **8xH100-SXM** | 8 | 128 | 1,600 GB | 640 GB | 8 TB SSD |

The **1xH100** is suited for development, single-GPU fine-tuning, and inference workloads. The **8xH100** is designed for large-scale training, multi-GPU inference, and distributed computing. Resources scale proportionally: the 8-GPU instance has 8x the CPU cores, memory, VRAM, and storage of the single-GPU instance.

## Multi-Node and InfiniBand

When you need to distribute a workload across multiple machines, provision 8xH100 instances in the same **sector**. Instances within a sector are connected over InfiniBand, providing ultra-low latency and high bandwidth for frameworks like PyTorch DDP, DeepSpeed, and Horovod.

> [!NOTE]
>
> InfiniBand and sector placement are only available on 8xH100 instances. 1xH100 instances run as standalone machines without inter-node connectivity.

## When to Use Compute vs Serverless

The two products serve different workload profiles:

| | Compute | Serverless |
| :- | :- | :- |
| **Billing** | Per-hour, fixed rate | Per-second of runner lifetime |
| **Scaling** | Manual (you manage instances) | Automatic (runners scale with traffic) |
| **Access** | Full SSH access to the machine | Code runs inside managed runners |
| **Best for** | Training, fine-tuning, batch jobs, research | API endpoints, on-demand inference, autoscaling |
| **Cold starts** | None (instance is always running) | Yes (new runners need startup time) |

Use Compute when you need sustained GPU access for hours or days at a time. Use Serverless when you need an API that scales to zero and handles traffic spikes automatically.

## Getting Started

Provisioning an instance takes about 2-3 minutes. You choose an instance type, select a sector (for multi-node setups), paste your SSH public key, and click create. Once the instance is ready, you SSH in and have full control.

> [card] [Quickstart](compute/quickstart.md)
>
> Provision your first instance and run a GPU workload

> [card] [Pricing](compute/pricing.md)
>
> Per-hour rates by instance type
