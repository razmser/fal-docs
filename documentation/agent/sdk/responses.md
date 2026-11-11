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

# Responses and recovery

> Submit tasks, observe response snapshots, and recover without creating duplicate work.

Use the [quickstart client](quickstart.md#create-a-client). Replace uppercase IDs with your resource IDs.

A response represents one task. Use its ID to observe, answer, or cancel that task.

## Methods

All methods below belong to the configured `agent` client.
`Response` in this table means `AgentResponseView`.

| Method | Result | Behavior |
| - | - | - |
| `run(request, options?)` | `Promise<Response>` | Creates a response, then polls until it stops or needs input. |
| `stream(request, options?)` | `AsyncIterable<Response>` | Creates a response, then streams snapshots. |
| `responses.create(request, options?)` | `Promise<Response>` | Returns the accepted response without waiting for completion. |
| `responses.retrieve(id, options?)` | `Promise<Response>` | Reads the current snapshot once. |
| `responses.wait(id, options?)` | `Promise<Response>` | Polls an existing response. |
| `responses.stream(id, options?)` | `AsyncIterable<Response>` | Streams an existing response. |
| `responses.answer(id, input, options?)` | `Promise<Response>` | Answers a pending input request. |
| `responses.cancel(id, options?)` | `Promise<Response>` | Requests cancellation of execution. |
| `responses.selectFinalArtifacts(id, input, options?)` | `Promise<Response>` | Saves explicit final artifact selections. |

## Request fields

| Field | Required | Description |
| - | - | - |
| `input` | Yes | A string of 1–100,000 characters, or an array containing one user message. |
| `conversation` | No | Adds a new response to this conversation. |
| `previous_response_id` | No | Continues from the latest settled response. Mutually exclusive with `conversation`. |
| `model` | No | Reasoning model ID from `models.listAgentModels()`. Overrides the default for this response. |
| `fal.generation_settings` | No | A generation settings snapshot. |
| `fal.generation_settings_overrides` | No | Generation setting overrides for this response. |
| `fal.skills` | No | Skill names to activate. Activated skills carry into later conversation turns. |
| `fal.on_ambiguity` | No | Only `"ask"` is supported. |

A structured user message has `role: "user"` and 1–40 content parts.
Supported parts are `input_text`, `input_image`, `input_file`, and `fal.input_artifact`.
Use `image_url` for images and `file_url` for files. See [media inputs](media.md).

Follow-ups create new response IDs. Answers continue the existing response ID.

## Options

| Option | Default | Applies to |
| - | - | - |
| `signal` | None | Local requests and observation. Aborting does not cancel server execution. |
| `timeoutMs` | No deadline | Positive finite deadline for the helper, including retries and observation. |
| `idempotencyKey` | Generated for mutations | Reuse the same key and body to recover a create, answer, or plan command. |
| `onAccepted` | None | `run()` and `stream()`. Called after acceptance, before observation. |
| `pollIntervalMs` | `1000` | Polling helpers. Must be nonnegative and finite. |
| `maxReconnects` | `3` | Streaming. Maximum consecutive failed or disconnected observation attempts. |
| `reconnectDelayMs` | `500` | Streaming. Delay between reconnect attempts. |

`selectFinalArtifacts` uses resource options: `signal` and `timeoutMs` only.
It uses `expected_sequence_number` to detect conflicting writes.

`maxReconnects` must be a nonnegative integer. `reconnectDelayMs` must be nonnegative and finite.
Idempotency keys must be nonempty, single-line strings with at most 200 characters.
`onAccepted` is synchronous. The helper does not await a promise returned by this callback.
Use `responses.create` when durable asynchronous storage must finish before observation starts.

## Read the response

| Field | Meaning |
| - | - |
| `id` | Stable response ID. |
| `status` | Execution status from the table below. |
| `output` | Ordered messages, operations, artifacts, and input requests. |
| `output_text` | Derived text from the output messages. |
| `artifacts` | Derived list of available artifacts. |
| `final_artifacts` | Artifacts explicitly selected as final results. |
| `pending_inputs` | Input requests that currently require an answer. |
| `error` | Response failure details, or `null`. |
| `fal.conversation_id` | Conversation that owns the response. |
| `fal.phase` | `queued`, `running`, `waiting_for_input`, `cancelling`, or `finished`. |
| `fal.sequence_number` | Cursor for changes reflected in this snapshot. |

| Status | Terminal | Meaning |
| - | - | - |
| `queued` | No | The task is waiting to run. |
| `in_progress` | No | The task is active or waiting for user input. |
| `completed` | Yes | The response completed. Inspect individual operations for handled generation failures. |
| `incomplete` | Yes | The response stopped before full completion. |
| `failed` | Yes | The response failed. Available artifacts can remain usable. |
| `cancelled` | Yes | The response was cancelled. |

Observation also stops at `fal.phase === "waiting_for_input"`, even when the status is not terminal.
After answering, resume observation with the same response ID.
Observation also stops when `fal.pending_submission` reports a saved answer that could not resume.
Follow [saved-answer recovery](inputs.md#recover-a-saved-answer-that-could-not-resume) before resuming observation.

`fal.model_usage` reports conversation model tokens across the response's turns.
Its `scope` is `"conversation_model"`.
The `input_tokens` and `output_tokens` fields are included only when every turn has reported that count.
These counts exclude media generation and auxiliary model calls. An absent field or `null` means unavailable, never zero.
`usage` is `null` when complete accounting is unavailable. Do not treat conversation model counts as a billing total.
`fal.pending_input_ids` and `fal.final_artifact_ids` reference items in `output`.
The convenience views are derived getters. `JSON.stringify(response)` omits them.
Read the getters explicitly when saving a presentation view.

## Example snapshot

This illustrative accepted response has no output yet. IDs vary for each request.
The derived fields `output_text`, `artifacts`, `final_artifacts`, and `pending_inputs` are added by the SDK.

```json theme={null}
{
  "id": "resp_example",
  "status": "queued",
  "output": [],
  "error": null,
  "usage": null,
  "fal": {
    "conversation_id": "conversation_example",
    "phase": "queued",
    "sequence_number": 0,
    "pending_input_ids": [],
    "final_artifact_ids": []
  },
  "output_text": "",
  "artifacts": [],
  "final_artifacts": [],
  "pending_inputs": []
}
```

### Output item types

| `type` | Contents | How to use it |
| - | - | - |
| `message` | Assistant content with `output_text` or `fal.block` parts. | Render text and supported blocks. Use a block's `fallback_text` for unknown kinds. |
| `fal.operation` | Operation status, artifact IDs, and an optional error or progress. | Show work in progress and handled generation failures. |
| `fal.artifact` | Completed media identity, revision, and optional files. | Display, download, or reference the media in a new task. |
| `fal.input_request` | A clarification or approval with its status and prompt. | Collect a decision from the user, then answer the request. |

Treat IDs as opaque strings. Response, input request, and artifact IDs identify different resources.

## Stream snapshots

The stream yields complete snapshots, not text deltas.
Replace the displayed state on each update. Do not append each snapshot's `output_text`.

```ts theme={null}
import { agent } from "./client.ts";

for await (const response of agent.responses.stream(
  "RESPONSE_ID",
)) {
  console.log(
    response.status,
    response.output_text,
  );
}
```

The SDK reconnects to existing work when an SSE connection closes.

## Recover an uncertain submission

Save the exact request body and idempotency key before the first attempt.

```ts theme={null}
import { agent } from "./client.ts";

const response = await agent.responses.create(
  { input: "Photograph a blue ceramic mug." },
  { idempotencyKey: "SAVED_COMMAND_KEY" },
);

console.log(response.id);
```

Retry an uncertain command with the same key and body.
A command key is scoped to your account and fal project, so replacing your API key does not create new work.
A new command key creates new work. A changed body with an existing command key returns a conflict.
`SAVED_COMMAND_KEY` represents the key stored before the original attempt. Use its exact saved input, too.

## Handle errors

Inspect `response.status` and `response.error` for execution failures.
Catch `AgentRequestError` for request failures.
See [errors and troubleshooting](errors.md) for recovery actions and error context.

## Cancel execution

```ts theme={null}
import { agent } from "./client.ts";

await agent.responses.cancel("RESPONSE_ID");
const response =
  await agent.responses.wait("RESPONSE_ID");
console.log(response.status);
```

Closing a stream, aborting a signal, or reaching a timeout only stops local observation.
Cancellation requests server and provider cancellation. It does not refund charges already incurred.
If cancellation fails, retrieve the same response and retry cancellation.
The response can remain active until cancellation is confirmed.
