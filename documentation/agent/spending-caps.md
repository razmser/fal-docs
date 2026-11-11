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
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Spending caps

> Confirm expensive generations before they run, per media type, with a safety cap in USD.

Spending caps stop the agent from starting an expensive run without your confirmation. They are configured in **Settings → Spending caps**.

![The Spending caps settings with the safety cap input and the Images, Videos, and Audio toggles](https://mintcdn.com/fal-d8505a2e/DmEWlE6WV9vaz0Me/images/agent/settings-spending-caps.png?fit=max\&auto=format\&n=DmEWlE6WV9vaz0Me\&q=85\&s=b74d5bb8138cbb222f0b9fc8cbe0ae6b)

## How the cap works

A run group asks for confirmation when both conditions are true:

1. The projected cost of the turn, including this run, is above your **safety cap**.
2. The run's media type has confirmation turned **on**.

Runs below the cap, and runs of a media type with confirmation off, start automatically. Runs with an unknown cost never ask.

## Defaults

| Setting | Default |
| :- | :- |
| **Confirm runs above** | \$5 |
| **Images** | Off. Usually a few cents per run |
| **Videos** | On. Often the priciest, from $0.30 to $10 or more per run |
| **Audio** | On. Cost varies with length and model |

## Cost estimates

The agent estimates cost from the model's price and from historical cost per request for that endpoint. The estimate is conservative. It uses a high percentile of past requests, so real costs are usually lower than the estimate. The agent also has a pricing tool and must check a model's real price before it quotes a cost to you.

## When a run is waiting

An approval alert appears in the chat with the estimated cost. Until you approve or reject it, the agent cannot submit further runs in that chat. A blocked submit shows the message "Blocked because a previous generation is awaiting cost approval." In a [model comparison](models-and-generation.md#model-comparison), waiting columns show **Awaiting approval**.

## The spend chip

Next to the composer, a chip shows the number of generations and the total cost of the chat. Hover it for a breakdown **By model** or **By modality**. A dot on the chip means some runs are not priced yet.

## Related controls

* [Plan card checkpoints](plan-cards.md#approval-checkpoints) gate on a step, not on cost.
* [Model access controls](../organizations/access-controls.md) restrict which models your organization can run at all.
