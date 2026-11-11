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

# Queue and steering

> Send messages while the agent works, queue follow-ups, stop a running chain, and cancel generations.

A chat runs one turn at a time. Everything else waits in a queue that you can see and edit. The queue has two lanes.

| Lane | How rows get there | Priority |
| :- | :- | :- |
| **Your messages** | You press `Enter` while a turn runs | Always run first, in order |
| **Queued work** | You press **Add to queue**, or the agent schedules a continuation or a plan step | Run after your messages |

## Send while the agent works

Press `Enter` while a turn is running. The message appears in the chat with an **Up next** badge and runs as soon as the current turn settles. The agent reads the queue before it continues, and it reconciles queued work that your new message makes stale. It can edit, remove, or reorder its own queued rows. It never touches your messages.

## Add to queue

Press `Alt+Enter` (`Option+Enter`) or click **Add to queue**. The message goes to the queued work lane. Use this for follow-ups that must wait for the current result, such as "then export everything as a zip".

## The queue drawer

The queue drawer sits behind the composer and lists **Queued requests**. It rises as rows are added.

For each row you can:

* Drag it to reorder, or use move up and move down.
* Edit its text.
* Cancel it.

A separator, **Runs after your requests**, marks where your pending messages end and queued work begins.

Each row shows a phase. **Waiting for source** means the row needs a previous run to land. **Approval waiting** means a checkpoint blocks it. **Chain halted** means you stopped the chain. **Ready** rows dispatch next.

## Plan steps in the queue

When a [plan](../plan-cards.md) runs, every remaining step becomes a queue row. Removing a step removes the steps that depend on it. Approval checkpoints hold their row until you approve.

## Stop

The send button becomes a stop button while a turn runs. `Escape` triggers it. Press `Escape` twice to confirm.

Stop escalates in two presses:

1. **Halt the chain.** Queued rows stay in the drawer but do not dispatch. Continuations from landed runs wait.
2. **Cancel generations.** In-flight runs are cancelled. Their cells show **Cancelled**. Dependent rows are parked with **Source run stopped** rather than deleted.

To resume a halted chain, send a message or click resume in the drawer. Resume clears the halt on every waiting row at once.

## Cancel one generation

Hover an in-flight cell and click the cancel button. Only that run stops. The rest of the batch continues.

## Cost approvals

A run that exceeds your [spending cap](../spending-caps.md) waits for approval. While it waits, the agent cannot submit more runs in that chat. Approve or reject it in the chat.
