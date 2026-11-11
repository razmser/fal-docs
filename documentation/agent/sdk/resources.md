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

# Conversations and history

> Create conversations, read their history, and manage their lifecycle.

Use the [quickstart client](quickstart.md#create-a-client). Replace uppercase IDs with your resource IDs.

## Conversations

| Method | Purpose |
| - | - |
| `conversations.create({ title }?, options?)` | Creates an idle conversation without running the agent. |
| `conversations.list(options?)` | Lists accessible conversations. |
| `conversations.retrieve(id, options?)` | Reads the title and active response IDs. |
| `conversations.update(id, { title }, options?)` | Renames the conversation. |
| `conversations.delete(id, options?)` | Starts or completes deletion. |
| `conversations.items.list(id, options?)` | Reads input, answer, and output history. |
| `conversations.generationSummary(id, options?)` | Reads generation cost information. |

```ts theme={null}
import { agent } from "./client.ts";
const conversation =
  await agent.conversations.create({
    title: "Product campaign",
  });
const response = await agent.run({
  conversation: conversation.id,
  input:
    "Suggest a visual direction. Do not generate media.",
});
console.log(conversation.id, response.id);
```

Conversation titles have a maximum of 120 characters. A rename requires a nonempty title after trimming.
`conversations.retrieve` returns `id`, `title`, and `active_response_ids`.

## Paginate history

Conversation and history list methods return `{ data, next_cursor }`.
Page options accept `cursor` and a `limit` from 1 to 100.

```ts theme={null}
import { agent } from "./client.ts";

let cursor: string | undefined;
do {
  const page = await agent.conversations.items.list(
    "CONVERSATION_ID",
    {
      cursor,
    },
  );
  console.log(page.data);
  cursor = page.next_cursor ?? undefined;
} while (cursor);
```

History items contain `id`, `response_id`, and `sequence_number`.
An item's `type` determines its payload. `input` contains a request input, `answer` contains an input request ID and answer, and `output` contains an output item.
The response ID can be `null`. See [AgentConversationItem](types.md#agentconversationitem).

History pagination returns `409` if the underlying snapshot changes.
Discard the accumulated pages and restart from the first page.

## Fork a conversation

```ts theme={null}
import { agent } from "./client.ts";

const fork = await agent.conversations.fork(
  "CONVERSATION_ID",
  {
    atMessageId: "MESSAGE_ID",
  },
);
console.log(fork.id);
```

The fork copies settled history through the selected message's completed turn.
Omit `atMessageId` to copy the available settled history. Active turns are excluded.
The optional `createProject` field controls project creation. Project placement completes asynchronously.

## Share a conversation

```ts theme={null}
import { agent } from "./client.ts";

const share =
  await agent.conversations.sharing.update(
    "CONVERSATION_ID",
    {
      emails: ["reviewer@example.com"],
    },
  );
console.log(
  `https://fal.ai/chat/share/${share.id}`,
);
```

This replaces the policy. Empty recipient lists allow anyone with the link to access the conversation.
Use `allowedAccountIds` for account access or `scopedAccountId` with `scopedEmails` for recipients within one account.
The optional `expiresAt` field accepts an ISO 8601 timestamp. Omitting it or passing `null` removes expiration.
Read the current policy with `conversations.sharing.retrieve(id)` before replacing it.
The result is `null` when no policy exists.

## Delete a conversation

Deletion can return `{ deleted: false }` while running work stops.
Repeat the deletion request to complete cleanup.

## Recover uncertain writes

Conversation creation is sent once. Rename and deletion can use transport retries.
Read the resource after a lost acknowledgement before deciding whether to repeat the write.

Request options support `signal` and `timeoutMs`.

## Related resources

* [Projects, documents, and memory](projects.md).
* [Models, settings, and preferences](settings.md).
* [Queues and generation runs](queue.md).
* [Library assets, collections, and entities](library.md).
* [Complete method signatures](methods.md).
