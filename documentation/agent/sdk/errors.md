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

# Errors and troubleshooting

> Distinguish request failures from execution failures and recover without duplicate work.

Use the [quickstart client](quickstart.md#create-a-client). Replace uppercase IDs with your resource IDs.

## Request errors

Inspect `response.status` and `response.error` for execution failures.
Catch `AgentRequestError` for transport or request failures.

```ts theme={null}
import { agent } from "./client.ts";
import { AgentRequestError } from "@fal-ai/client";

try {
  await agent.responses.retrieve("RESPONSE_ID");
} catch (error) {
  if (!(error instanceof AgentRequestError))
    throw error;
  console.error(
    error.status,
    error.message,
    error.responseId,
  );
}
```

| Error field | Meaning |
| - | - |
| `message` | Request failure description. |
| `status` | Optional HTTP status. |
| `responseId` | Accepted or observed response identity when known. |
| `idempotencyKey` | Mutation key when available. |
| `lastResponse` | Last observed snapshot when available. |
| `cause` | Underlying transport, abort, timeout, or protocol error. |

Error context is optional. An absent HTTP status can indicate a local timeout, abort, or network failure.

| HTTP status | Action |
| - | - |
| `400` | Correct the request or cursor using the returned error message. |
| `401` / `403` | Check your credentials and account access. |
| `404` | Check the resource ID and access permissions. |
| `409` | Retrieve current state. Resolve the revision, cursor, or idempotency conflict before retrying. |
| `410` | The retained response is no longer available. |
| `422` | Correct the input using the returned error message. |

## Local and protocol errors

Invalid options or IDs can throw `TypeError` before a request starts.
Correct the argument instead of retrying it.

`AgentProtocolError` indicates an invalid snapshot, response identity, or stream envelope.
It can appear inside an `AgentRequestError.cause` chain.
Retrieve the response by its saved ID.
`AgentEvent` describes the transport envelope. `agent.stream` yields response views, not raw event objects.

## Choose a recovery action

| Situation | What happened | Next action |
| - | - | - |
| `run()` resolved with `failed` | The request was accepted, but execution failed. | Read `response.error` and any available artifacts before deciding on a new task. |
| A helper returned `waiting_for_input` | The response needs an answer or approval. | Read `pending_inputs`, answer, then resume observation of the same ID. |
| Create or answer lost its acknowledgement | The server might have accepted the command. | Retry the exact body with the same idempotency key. |
| A stream disconnected | Local observation stopped. Execution can continue. | Reconnect with `responses.stream(id)` or retrieve the response. |
| A local timeout expired | The helper reached its observation deadline. | Retrieve the response. Call `responses.cancel` to stop execution. |
| A plan or final-selection write returned `409` | The expected revision or sequence no longer matches. | Read the latest state and apply your changes to that version. |
| A history page returned `409` | The history snapshot changed. | Discard accumulated pages and restart pagination. |
| A resource write lost its acknowledgement | The resource could have changed. | Read it back before retrying. A run retry can create another paid attempt. |

## Rate limits and service failures

For `429`, reduce concurrent requests and avoid immediate retry loops.
For `5xx` or network failures, allow time between attempts.
When retrying a submission, preserve its original idempotency key and body.

Correct validation or permission errors before retrying.

## Capture useful diagnostics

Record the SDK version, method, HTTP status, error message, and response ID when available.
For execution failures, record the response status and phase.

Remove credentials and private prompt content before sharing logs.
