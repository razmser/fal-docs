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

# Models and generation

> How fal Agent chooses models, validates inputs, runs batches, compares models, and repairs failed runs.

Every generation in fal Agent is a normal fal model run. The agent adds the parts that are usually manual: model choice, batching, and repair.

## The model catalog

The agent works from the live fal model registry. The catalog refreshes every few minutes, so new models are available soon after they launch. The catalog is filtered by your account's access policy. Restricted models are not visible to the agent and cannot be run by mention.

If a model is not in the catalog by name, the agent can search the registry for it.

## Auto model choice

When you do not pin a model, the composer shows an **Auto** chip and the agent chooses. The choice follows fal's current recommendations for the task, the media type, the resolution, and whether references are attached. The recommendations change as new models launch, so the agent stays current without a settings change on your side.

To pin a model, type `@` in the composer and pick one. You can also pin a model on one step of a [plan card](plan-cards.md). A pinned model is final. The agent does not substitute another model for it.

## Batches

A batch is two or more runs submitted together. Batches render as one grid in the chat. The agent uses batches for:

* **Variations.** Several takes on one idea, spread across two or three aspect ratios.
* **Candidates.** The same prompt on several models to pick the best.
* **Sets.** A campaign with one output per format.

Each run in a batch can carry a name. The name becomes the `#` reference token in the composer and the folder path in an [export](generations/export.md).

## Model comparison

Ask the agent to compare two or more models on one prompt. The result is a comparison card with one column per model. Each column shows the generation, the model name, and a spec line with latency, estimated price, resolution, and seed. **Fastest** and **cheapest** labels are computed from the results. The card does not give a verdict. You decide.

## Repair after failure

When a run fails after submission, the agent receives the failure and diagnoses it. Typical repairs are:

* **Input constraints.** An oversized reference image is resized in the sandbox and resubmitted.
* **Input mismatch.** The input is rebuilt to match what the model expects.
* **Transient provider errors.** The run is retried once.

The agent repairs a run up to three times. After that, it explains the failure and asks how to proceed. Retried runs are paginated in the cell as **Attempt N of M**.

## Errors you can see

Failed cells show a short explanation. Some messages are rewritten for clarity. The raw provider message stays available on hover.

| Message | Meaning |
| :- | :- |
| This model isn't available on your account. | Your access policy restricts the model |
| Insufficient credits. Add credits to your account and try again. | Your balance is too low for the run |
| Account locked — generations are paused. | Billing needs attention. Check the [Billing](https://fal.ai/dashboard/billing) page |
| Couldn't reach the model service. Retry to try again. | A transient failure on the provider side |
| Cancelled | You stopped the run. This is not a failure |

## Cancellation

Hover an in-flight cell and click the cancel button to stop that run. The cell shows **Cancelled**. To stop everything, use the stop button in the composer. See [Queue and steering](chats/queue.md).

## Limits

| Limit | Value |
| :- | :- |
| Async repairs per run | 3 |
| Tool steps per turn | About 60. Longer work continues in the next turn |
| Concurrent turns per chat | 1 |

When a turn needs more steps than the limit, the agent stops and tells you. Send another message to continue.
