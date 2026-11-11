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

# Queues and generation runs

> Control queued work, handle generation approvals, and inspect or retry a generation attempt.

Use the [quickstart client](quickstart.md#create-a-client). Replace uppercase IDs with your resource IDs.

A response represents an agent task. A queued turn represents work within a conversation.
A generation run represents one media operation. Use each resource's own ID with its methods.

## Inspect a queue

```ts theme={null}
import { agent } from "./client.ts";

const queue = await agent.queue.retrieve(
  "CONVERSATION_ID",
);
console.log(queue.items, queue.active_turn_ids);
```

| Queue item field | Meaning |
| - | - |
| `turnId` | ID for queue actions. |
| `qlane` | `user` for user work or `drawer` for continuation work. |
| `kind` | `prompt` or `continuation`. |
| `prompt`, `stepLabel` | Optional prompt text and step label. |
| `position` | Queue ordering value. |
| `requiresApproval` | Whether an approval hold blocks the turn. |
| `parked` | Whether the turn is held outside normal dispatch. |
| `halted` | Whether dispatch is paused for the turn. |
| `planStep` | Optional plan block, execution, step ID, and step order. |

`active_turn_ids` identifies running turns. It does not contain response IDs.
Submit tasks through responses or run a plan to add work to the queue.

## Pause, edit, and resume

```ts theme={null}
import { agent } from "./client.ts";

await agent.queue.setHalted(
  "CONVERSATION_ID",
  true,
);
await agent.queue.edit(
  "CONVERSATION_ID",
  "TURN_ID",
  "Use a blue background.",
);
```

Halting pauses dispatch. It does not cancel work already running.
`setHalted` returns `updatedCount` and an optional `dispatch` result.

| Method | Input and behavior |
| - | - |
| `queue.reorder(conversationId, turnIds)` | Reorder up to 50 queued turn IDs. |
| `queue.edit(conversationId, turnId, content)` | Replace a queued prompt with 1–10,000 characters. |
| `queue.cancel(conversationId, turnId)` | Cancel the selected queued turn. |
| `queue.setHalted(conversationId, false)` | Resume dispatch and attempt to start eligible work. |
| `queue.dispatch(conversationId)` | Attempt to start eligible queued work. |
| `queue.run(conversationId, turnId)` | Request immediate execution of a selected queued turn. |

Steps within a plan retain their required order.
Queued messages from the user dispatch first.
Retrieve the queue after reordering to read its
effective order.

Reorder, edit, and cancel return `{ success }`.
If a turn starts before your edit, retrieve the queue again.
Use `responses.cancel` to cancel the task, or `runs.cancel` to cancel a generation run.

## Read dispatch results

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.queue.dispatch(
  "CONVERSATION_ID",
);
if (result.promoted) console.log(result.turnId);
else
  console.log(result.reason ?? "No turn started.");
```

| Field | Meaning |
| - | - |
| `promoted` | Whether the call started a turn. |
| `turnId` | Turn identity when available. |
| `assistantMessageId` | Associated assistant message identity when available. |
| `requeued` | Whether work returned to the queue after a failed dispatch attempt. |
| `reason` | Explanation when available. Treat it as an open string, not a fixed enum. |

A successful HTTP request can return `promoted: false`.
Inspect the queue for running work, approval holds, or a halted state.

A queue change can succeed while its dispatch attempt fails.
In that case, retrieve the queue before calling `queue.dispatch` again.

## Release an approval hold

Set `requiresApproval` to `false` to release a queued continuation's hold:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.queue.setApproval(
  "CONVERSATION_ID",
  "TURN_ID",
  {
    requiresApproval: false,
  },
);

console.log(result.updated, result.dispatch);
```

Set `requiresApproval: true` to add a hold.
A false value can dispatch work immediately.
If `updated` is false, the turn can have started or been cancelled. Retrieve the queue again.

`approveCheckpoints: true` also approves the relevant plan checkpoints and can release related continuations.
Prefer [response approvals](inputs.md) when responding to `response.pending_inputs`.

## Inspect a generation run

For an operation with `kind: "generation"`, use its `id` as the run ID. Pass its owning conversation ID as the second argument.

```ts theme={null}
import { agent } from "./client.ts";

const run = await agent.runs.retrieve(
  "RUN_ID",
  "CONVERSATION_ID",
);
console.log(
  run.operation.status,
  run.artifacts,
  run.input_requests,
);
```

The result contains an `operation`, available `artifacts`, and cost approval `input_requests`.
The operation includes its status, artifact IDs, optional progress, and an error when it fails.
A failed run can still have useful artifacts.

## Retry a generation

A retry creates another generation attempt and can incur charges.

```ts theme={null}
import { agent } from "./client.ts";

const retry = await agent.runs.retry(
  "RUN_ID",
  "CONVERSATION_ID",
);
console.log(
  retry.runId,
  retry.attempt,
  retry.approvalRequired,
);
```

The result contains `mediaId`, `runId`, `attempt`, `requestId`, and optional `approvalRequired`.
Use the returned run ID to inspect the attempt.
If it requires approval, retrieve the run and answer its pending input request.

## Answer a cost approval

Use the input request ID from the retrieved run.

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.runs.answer(
  "RUN_ID",
  "CONVERSATION_ID",
  {
    input_request_id: "INPUT_REQUEST_ID",
    decision: "approve",
  },
);

console.log(
  result.submittedCount,
  result.failedCount,
  result.cancelledCount,
);
```

Use `reject` to decline. The decision applies to the request's approval group, which can contain multiple generations.
The result contains optional counts. Missing counts are not evidence of zero work.
On `409`, retrieve the run again and use the current input request for the next decision.

## Cancel a run

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.runs.cancel(
  "RUN_ID",
  "CONVERSATION_ID",
);
console.log(result.cancelled);
```

Inspect the run after cancellation. A cancellation does not refund charges already incurred.
`cancelled: false` does not confirm that the provider stopped.
If the run is still generating, retrieve its latest state and retry cancellation.
Queue and run methods accept optional `signal` and `timeoutMs` options.
Writes are sent once and are not retried automatically.

After an uncertain retry or approval, retrieve the run before sending another request.
See [errors and troubleshooting](errors.md) for request failures and execution failures.
