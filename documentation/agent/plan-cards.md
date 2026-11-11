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

# Plan cards

> Review, edit, approve, and version the agent's plan before and during a multi-step run.

A plan card is the agent's proposal for multi-step work. It appears in the chat as a titled, numbered list of steps. Each step can carry a model chip, a short reason, and an approval checkpoint. A **Why this plan** disclosure explains the overall approach.

Simple requests do not produce a plan card. The agent runs them directly.

## Read the card

| Element | Meaning |
| :- | :- |
| Step label | What the step produces |
| Model chip | The model the agent intends to use. Empty means Auto |
| Reason | One line on why this step or model |
| **Pause here for approval** | The run stops before this step until you approve it |
| Status badge | **Running**, **Checking status…**, or **Another plan is running** |

## Edit the plan

You can edit the card directly before you run it, and you can edit the remaining steps while it runs.

* **Rename** a step by clicking its label.
* **Reorder** steps by dragging them.
* **Add** or **remove** steps with the controls on each row. Removing a step also removes the steps that depend on it.
* **Pin a model** on a step by clicking its model chip. A pinned model is final. The agent does not replace it.
* **Toggle an approval checkpoint** on any step.

The agent sees your edits. When it continues, it works from the edited plan, not its original proposal.

## Run, cancel, or revise

* **Run plan** starts execution. Every remaining step becomes a visible row in the [queue](chats/queue.md), so you can see the whole tail.
* **Cancel plan** tells the agent you do not want this plan. The agent treats it as feedback and proposes a revision.
* **Not happy with the plan?** Type a change in the text box on the card. This sends a normal message and produces a new plan version.

Keyboard: `Cmd+Enter` (`Ctrl+Enter`) submits the revision box.

## Approval checkpoints

A checkpoint is a hard stop. The agent cannot run past an unapproved checkpoint, even if it wants to. When execution reaches one, the chat row in the sidebar shows **Needs approval**, and the queue row waits. Approve it on the card to continue. Approval is consumed once, so the same step does not ask twice.

Checkpoints are separate from [spending caps](spending-caps.md), which gate on cost.

## Plan versions

One chat has one logical plan with a version history.

* A re-render in the same turn updates the card in place.
* A re-plan in a later turn creates a new card, **v2**, **v3**, and so on, at the bottom of the chat.
* The old card freezes as superseded and shows **Jump to the latest plan version**.

## Steps that continue automatically

When a step's run lands, the agent continues with the next step without a message from you. The card shows the next step as **Up next**. If a batch lands partially, the agent continues with the runs that succeeded and reports the rest.

You can interrupt at any time. See [Queue and steering](chats/queue.md) for how a message sent mid-run is handled, and how to halt or resume a chain.
