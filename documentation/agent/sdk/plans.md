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

# Edit and run plans

> Update plan steps with revision checks and approval checkpoints.

Use the [quickstart client](quickstart.md#create-a-client). Replace uppercase IDs with your plan and conversation IDs.
The `SAVED_*_KEY` values represent persisted keys for those commands.

A plan is a `fal.block` with `kind: "plan"` inside an assistant message.
Use its ID with `agent.plans`. Pass the owning conversation ID with every plan request.

## Methods

| Method | Result |
| - | - |
| `plans.retrieve(id, { conversation, ...options })` | Current `AgentPlanBlock`. |
| `plans.update(id, change, options?)` | Updated `AgentPlanBlock` with its revision. |
| `plans.run(id, { conversation, expected_revision }, options?)` | Accepted `AgentResponseView`. |

`update` and `run` accept an `idempotencyKey` through their options.
Persist one key for each logical command before sending it.

## Update the editable steps

An update replaces the complete editable step list.
Keep existing IDs to preserve steps. Omit an ID to add a step.
Remove a step by omitting it from the array. Change execution order by reordering the array.

| Field | Requirement |
| - | - |
| `conversation` | Owning conversation ID. |
| `expected_revision` | Revision returned by the latest plan read. |
| `title` | Optional nonempty title, up to 120 characters. |
| `steps` | Complete array of 1–50 editable steps. |
| `steps[].id` | Existing step ID, or omitted for a new step. Existing IDs must be unique. |
| `steps[].label` | Step instruction of 1–500 characters after trimming. |
| `steps[].endpoint_id` | Model endpoint of 1–200 characters, or `null` to clear it. |
| `steps[].model_pinned` | Required boolean. A pinned model requires an endpoint. |
| `steps[].requires_approval` | Required boolean for the approval checkpoint. |

This example replaces the plan with one new step.

```ts theme={null}
import { agent } from "./client.ts";

const conversation = "CONVERSATION_ID";
const plan = await agent.plans.retrieve("PLAN_ID", {
  conversation,
});

await agent.plans.update(
  plan.id,
  {
    conversation,
    expected_revision: plan.revision,
    steps: [
      {
        label: "Generate a product photo",
        model_pinned: false,
        requires_approval: true,
      },
    ],
  },
  { idempotencyKey: "SAVED_EDIT_KEY" },
);
```

A stale revision or an edit during execution returns `409`.
Retrieve the latest plan and apply your changes to that version before retrying.

## Run a plan

Send the revision you want to run. In this example, that revision is `3`.

```ts theme={null}
import { agent } from "./client.ts";

const response = await agent.plans.run(
  "PLAN_ID",
  {
    conversation: "CONVERSATION_ID",
    expected_revision: 3,
  },
  { idempotencyKey: "SAVED_RUN_KEY" },
);

console.log(response.id);
```

The returned response can pause at an approval checkpoint.
Use the [approval flow](inputs.md) to continue that response.
