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

# Questions and approvals

> Answer questions and approvals, then resume the same Agent response.

Use the [quickstart client](quickstart.md#create-a-client). Replace uppercase IDs with values from the pending request.
The `SAVED_*_KEY` values represent persisted keys for those answers.

When `response.fal.phase` is `waiting_for_input`, read `response.pending_inputs` for the questions or approvals that need an answer.

## Answer a clarification

Render each question's text and options using its `multiple` and `allow_text` fields. Use IDs from the pending input request.

```ts theme={null}
import { agent } from "./client.ts";

await agent.responses.answer(
  "RESPONSE_ID",
  {
    input_request_id: "INPUT_REQUEST_ID",
    answer: {
      kind: "answers",
      answers: [
        {
          question_id: "QUESTION_ID",
          selected_option_ids: ["OPTION_ID"],
        },
      ],
    },
  },
  { idempotencyKey: "SAVED_ANSWER_KEY" },
);
```

Answer every question exactly once. Select only the returned option IDs, without duplicates.
When `multiple` is false, select at most one option.
Include free text only when `allow_text` is true.
Each answer requires an option or nonempty text.
Submit 1–20 answers, with at most 20 selected options per question and 2,000 characters of text.

Model preference questions use the same answer format. Select the returned option IDs.
The answer applies the requested preference change before execution continues.

## Submit an approval decision

An approval identifies its target with `target.item_id` and `target.revision`.
The `accepted_answers` field lists the permitted decisions for that target.

```ts theme={null}
import { agent } from "./client.ts";

await agent.responses.answer(
  "RESPONSE_ID",
  {
    input_request_id: "INPUT_REQUEST_ID",
    answer: {
      kind: "approval",
      decision: "approve",
    },
  },
  { idempotencyKey: "SAVED_APPROVAL_KEY" },
);
```

Send `approve` to allow the requested action or `reject` to decline it.

Input request states are `pending`, `answered`, `rejected`, `expired`, and `cancelled`.
An optional `expires_at` gives the expiry time. Only pending requests can accept an answer.

## Resume and recover

After answering, call `responses.wait(id)` or `responses.stream(id)` with the same response ID. The agent can request another input.

If the acknowledgement is lost, retry with the exact answer body and idempotency key.
If the input is stale, retrieve the response and render its current pending inputs.

### Recover a saved answer that could not resume

An answer can be saved before the next turn starts.
If that turn fails to start, `fal.pending_submission` contains the `input_request_id`, `action: "retry_input"`, and an `error`.
The response stays unfinished. Polling and streaming helpers return the snapshot.

Fix the reported problem. Retry `responses.answer` with the exact saved body and idempotency key.
This resumes the saved answer without applying the decision twice. Observation alone does not retry submission.
