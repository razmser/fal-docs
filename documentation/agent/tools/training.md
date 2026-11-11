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
> - [Pricing](../../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Training

> Fine-tune a LoRA on your own images from inside a chat, and follow the run with a live loss curve and sample outputs.

> [!NOTE]
>
> Training is in limited availability. Contact your fal representative to enable it for your account. The **training** skill and the training card appear only for accounts with access.

Training lets you teach a model your subject or style. You provide a dataset, and the agent runs a LoRA fine-tune on a base model. The result is a model you can use in later generations.

## Start a run

Attach a ZIP of training images, up to 2 GB, and describe the goal:

```text theme={null}
Train a LoRA on these 30 product photos so I can place the bottle in new scenes. Trigger word: ACMEBTL.
```

The agent chooses the training settings for the base model. Step count and learning rate are set server side and cannot be changed in the chat. You can set a trigger phrase, which the agent uses verbatim.

## The training card

A training card appears in the chat with:

* A status badge and a progress bar with current and total steps
* An estimated time to finish
* A **Training loss** chart that updates as the run proceeds
* Sample outputs from the model at checkpoints
* A download action for the trained weights

The agent checks progress for you and reports when the run finishes, with a recommendation on whether the result looks ready to use.

## Use the result

Once training completes, ask the agent to generate with the trained model and the trigger phrase. The model is available to your account like any other fal model.
