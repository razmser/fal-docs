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

# Method reference

> Signatures, inputs, return types, and guide links for every Agent client method.

All methods belong to the configured `agent` client. A `?` marks an optional argument or field.
Signatures describe types. Examples import the quickstart client. Replace uppercase IDs, file URLs, saved keys, and example revisions before execution.

Read the [request limits](responses.md#request-fields) before constructing an `AgentRequest`.
See [client configuration](configuration.md) for setup and retry behavior.

## Responses

### responses.answer

Answer a pending clarification or approval. Use the request ID and permitted answers from the current pending input.

```typescript Signature theme={null}
(
  id: string,
  input: {
    input_request_id: string;
    answer: AgentAnswer;
  },
  options?: AgentRequestOptions,
) => Promise<AgentResponseView>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.responses.answer(
  "RESPONSE_ID",
  {
    input_request_id: "INPUT_REQUEST_ID",
    answer: {
      kind: "approval",
      decision: "approve",
    },
  },
  { idempotencyKey: "SAVED_ANSWER_KEY" },
);
console.log(result);
```

Guide: [responses](responses.md). Types: [AgentAnswer](types.md#agentanswer), [AgentRequestOptions](types.md#agentrequestoptions), [AgentResponseView](types.md#agentresponseview).

### responses.cancel

Request server cancellation. Observe the same response to confirm its final state.

```typescript Signature theme={null}
(id: string, options?: AgentRequestOptions) =>
  Promise<AgentResponseView>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.responses.cancel("RESPONSE_ID");
console.log(result);
```

Guide: [responses](responses.md). Types: [AgentRequestOptions](types.md#agentrequestoptions), [AgentResponseView](types.md#agentresponseview).

### responses.create

Submit a task and return its accepted snapshot. Save the response ID before observing execution.

```typescript Signature theme={null}
(
  request: AgentRequest,
  options?: AgentRequestOptions,
) => Promise<AgentResponseView>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.responses.create(
  { input: "Generate a blue ceramic mug." },
  { idempotencyKey: "SAVED_COMMAND_KEY" },
);
console.log(result);
```

Guide: [responses](responses.md). Types: [AgentRequest](types.md#agentrequest), [AgentRequestOptions](types.md#agentrequestoptions), [AgentResponseView](types.md#agentresponseview).

### responses.retrieve

Read one response snapshot. This method does not wait for execution to finish.

```typescript Signature theme={null}
(id: string, options?: AgentRequestOptions) =>
  Promise<AgentResponseView>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.responses.retrieve("RESPONSE_ID");
console.log(result);
```

Guide: [responses](responses.md). Types: [AgentRequestOptions](types.md#agentrequestoptions), [AgentResponseView](types.md#agentresponseview).

### responses.selectFinalArtifacts

Save the final artifact selection. Send the current sequence number to detect conflicting writes. Replace the example cursor with the current snapshot sequence.

```typescript Signature theme={null}
(
  id: string,
  input: {
    artifact_ids: string[];
    expected_sequence_number: number;
  },
  options?: AgentResourceOptions,
) => Promise<AgentResponseView>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.responses.selectFinalArtifacts(
    "RESPONSE_ID",
    {
      artifact_ids: ["ARTIFACT_ID"],
      expected_sequence_number: 12,
    },
  );
console.log(result);
```

Guide: [responses](responses.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentResponseView](types.md#agentresponseview).

### responses.stream

Stream complete snapshots of an existing response. Replace displayed state on each update.

```typescript Signature theme={null}
(id: string, options?: AgentStreamOptions) =>
  AsyncIterable<AgentResponseView>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

for await (const response of agent.responses.stream(
  "RESPONSE_ID",
)) {
  console.log(response.output_text);
}
```

Guide: [responses](responses.md). Types: [AgentStreamOptions](types.md#agentstreamoptions), [AgentResponseView](types.md#agentresponseview).

### responses.wait

Poll an existing response until it stops or requires input. A timeout only stops local observation.

```typescript Signature theme={null}
(id: string, options?: AgentRunOptions) =>
  Promise<AgentResponseView>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.responses.wait(
  "RESPONSE_ID",
  { timeoutMs: 60_000 },
);
console.log(result);
```

Guide: [responses](responses.md). Types: [AgentRunOptions](types.md#agentrunoptions), [AgentResponseView](types.md#agentresponseview).

### run

Create a response and poll until execution stops or requires input. This method can incur generation charges.

```typescript Signature theme={null}
(
  request: AgentRequest,
  options?: AgentRunOptions,
) => Promise<AgentResponseView>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.run({
  input: "Generate a blue ceramic mug.",
});
console.log(result);
```

Guide: [responses](responses.md). Types: [AgentRequest](types.md#agentrequest), [AgentRunOptions](types.md#agentrunoptions), [AgentResponseView](types.md#agentresponseview).

### stream

Create a response and yield complete snapshots. This method starts new work each time you call it.

```typescript Signature theme={null}
(
  request: AgentRequest,
  options?: AgentStreamOptions,
) => AsyncIterable<AgentResponseView>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

for await (const response of agent.stream({
  input: "Generate a blue ceramic mug.",
})) {
  console.log(response.output_text);
}
```

Guide: [responses](responses.md). Types: [AgentRequest](types.md#agentrequest), [AgentStreamOptions](types.md#agentstreamoptions), [AgentResponseView](types.md#agentresponseview).

## Conversations

### conversations.create

Create an idle conversation. An omitted title or null title leaves the conversation untitled.

```typescript Signature theme={null}
(
  input?: { title?: string | null },
  options?: AgentResourceOptions,
) => Promise<AgentConversation>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.conversations.create({
  title: "Product campaign",
});
console.log(result);
```

Guide: [resources](resources.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentConversation](types.md#agentconversation).

### conversations.delete

Delete a conversation. Repeat the request when deleted is false, after running work stops.

```typescript Signature theme={null}
(id: string, options?: AgentRequestOptions) =>
  Promise<{ id: string; deleted: boolean }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.conversations.delete(
  "CONVERSATION_ID",
);
console.log(result);
```

Guide: [resources](resources.md). Types: [AgentRequestOptions](types.md#agentrequestoptions).

### conversations.fork

Copy settled conversation history into a new conversation. Set atMessageId to copy through that message's completed turn. Active turns are excluded. Project placement completes asynchronously.

```typescript Signature theme={null}
(
  id: string,
  input?: {
    atMessageId?: string;
    createProject?: boolean;
  },
  options?: AgentResourceOptions,
) => Promise<AgentConversation>;
```

Example:

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

Guide: [resources](resources.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentConversation](types.md#agentconversation).

### conversations.generationSummary

Read billed generation costs and counts. Nano-USD totals exclude unpriced requests and LLM usage.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentGenerationSummary>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.conversations.generationSummary(
    "CONVERSATION_ID",
  );
console.log(result);
```

Guide: [resources](resources.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentGenerationSummary](types.md#agentgenerationsummary).

### conversations.items.list

Read conversation history with cursor pagination. Restart pagination after a snapshot conflict.

```typescript Signature theme={null}
(id: string, options?: AgentPageOptions) =>
  Promise<AgentPage<AgentConversationItem>>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.conversations.items.list(
  "CONVERSATION_ID",
  { limit: 20 },
);
console.log(result);
```

Guide: [resources](resources.md). Types: [AgentPageOptions](types.md#agentpageoptions), [AgentPage](types.md#agentpage), [AgentConversationItem](types.md#agentconversationitem).

### conversations.list

List conversations with cursor pagination. Use next\_cursor for the next page.

```typescript Signature theme={null}
(options?: AgentPageOptions) =>
  Promise<AgentPage<AgentConversation>>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.conversations.list({
  limit: 20,
});
console.log(result);
```

Guide: [resources](resources.md). Types: [AgentPageOptions](types.md#agentpageoptions), [AgentPage](types.md#agentpage), [AgentConversation](types.md#agentconversation).

### conversations.retrieve

Read a conversation's title and active response IDs.

```typescript Signature theme={null}
(id: string, options?: AgentRequestOptions) =>
  Promise<AgentConversation>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.conversations.retrieve(
  "CONVERSATION_ID",
);
console.log(result);
```

Guide: [resources](resources.md). Types: [AgentRequestOptions](types.md#agentrequestoptions), [AgentConversation](types.md#agentconversation).

### conversations.sharing.retrieve

Read the conversation's share policy, or null if none exists. The policy ID identifies its /chat/share/{id} link on the fal website. Empty recipient lists allow anyone with the link.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentConversationSharePolicy | null>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const share =
  await agent.conversations.sharing.retrieve(
    "CONVERSATION_ID",
  );
console.log(share?.policy);
```

Guide: [resources](resources.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentConversationSharePolicy](types.md#agentconversationsharepolicy).

### conversations.sharing.update

Create or replace the conversation's share policy. Specify account IDs or email addresses to restrict access. Empty recipients make the link public. expiresAt accepts an ISO 8601 timestamp; omit it or pass null to remove expiration.

```typescript Signature theme={null}
(
  id: string,
  input: AgentConversationSharingInput,
  options?: AgentResourceOptions,
) => Promise<AgentConversationSharePolicy>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const share =
  await agent.conversations.sharing.update(
    "CONVERSATION_ID",
    { emails: ["reviewer@example.com"] },
  );
console.log(share.id);
```

Guide: [resources](resources.md). Types: [AgentConversationSharingInput](types.md#agentconversationsharinginput), [AgentResourceOptions](types.md#agentresourceoptions), [AgentConversationSharePolicy](types.md#agentconversationsharepolicy).

### conversations.update

Rename a conversation. Read it after an uncertain acknowledgement before repeating the write.

```typescript Signature theme={null}
(
  id: string,
  change: { title: string },
  options?: AgentRequestOptions,
) => Promise<AgentConversation>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.conversations.update(
  "CONVERSATION_ID",
  { title: "Mug concepts" },
);
console.log(result);
```

Guide: [resources](resources.md). Types: [AgentRequestOptions](types.md#agentrequestoptions), [AgentConversation](types.md#agentconversation).

## Plans

### plans.retrieve

Read a plan block within its owning conversation.

```typescript Signature theme={null}
(
  id: string,
  options: AgentRequestOptions & {
    conversation: string;
  },
) => Promise<AgentPlanBlock>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.plans.retrieve(
  "PLAN_ID",
  { conversation: "CONVERSATION_ID" },
);
console.log(result);
```

Guide: [plans](plans.md). Types: [AgentRequestOptions](types.md#agentrequestoptions), [AgentPlanBlock](types.md#agentplanblock).

### plans.run

Run a plan at its current revision. Replace the example revision with the value returned by `plans.retrieve`. Save an idempotency key before submission.

```typescript Signature theme={null}
(
  id: string,
  input: {
    conversation: string;
    expected_revision: number;
  },
  options?: AgentRequestOptions,
) => Promise<AgentResponseView>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.plans.run(
  "PLAN_ID",
  {
    conversation: "CONVERSATION_ID",
    expected_revision: 3,
  },
  { idempotencyKey: "SAVED_PLAN_KEY" },
);
console.log(result);
```

Guide: [plans](plans.md). Types: [AgentRequestOptions](types.md#agentrequestoptions), [AgentResponseView](types.md#agentresponseview).

### plans.update

Replace the editable step list at the specified revision. Preserve step IDs to retain existing steps. The example replaces the complete list with one step at revision 3.

```typescript Signature theme={null}
(
  id: string,
  change: AgentPlanUpdate,
  options?: AgentRequestOptions,
) => Promise<AgentPlanBlock>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.plans.update(
  "PLAN_ID",
  {
    conversation: "CONVERSATION_ID",
    expected_revision: 3,
    steps: [
      {
        id: "STEP_ID",
        label: "Generate a product photo",
        model_pinned: false,
        requires_approval: true,
      },
    ],
  },
  { idempotencyKey: "SAVED_EDIT_KEY" },
);
console.log(result);
```

Guide: [plans](plans.md). Types: [AgentPlanUpdate](types.md#agentplanupdate), [AgentRequestOptions](types.md#agentrequestoptions), [AgentPlanBlock](types.md#agentplanblock).

## Artifacts

### artifacts.retrieve

Read an artifact. Omit revision for the available current version.

```typescript Signature theme={null}
(
  id: string,
  options?: AgentRequestOptions & { revision?: 1 },
) => Promise<AgentArtifact>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.artifacts.retrieve("ARTIFACT_ID");
console.log(result);
```

Guide: [media](media.md). Types: [AgentRequestOptions](types.md#agentrequestoptions), [AgentArtifact](types.md#agentartifact).

## Projects

### projects.assets.attach

Attach an existing asset to a project. Use its assetId or library assetRecordId.

```typescript Signature theme={null}
(
  id: string,
  assetId: string,
  options?: AgentResourceOptions,
) => Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.projects.assets.attach(
  "PROJECT_ID",
  "ASSET_ID",
);
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### projects.assets.detach

Detach an asset from a project without deleting its source file.

```typescript Signature theme={null}
(
  id: string,
  assetId: string,
  options?: AgentResourceOptions,
) => Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.projects.assets.detach(
  "PROJECT_ID",
  "ASSET_ID",
);
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### projects.collections.attach

Attach an existing collection to a project.

```typescript Signature theme={null}
(
  id: string,
  collectionId: string,
  options?: AgentResourceOptions,
) => Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.collections.attach(
    "PROJECT_ID",
    "COLLECTION_ID",
  );
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### projects.collections.detach

Remove a collection's project association.

```typescript Signature theme={null}
(
  id: string,
  collectionId: string,
  options?: AgentResourceOptions,
) => Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.collections.detach(
    "PROJECT_ID",
    "COLLECTION_ID",
  );
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### projects.conversations.add

Add an existing conversation to a project.

```typescript Signature theme={null}
(
  id: string,
  conversation: string,
  options?: AgentResourceOptions,
) => Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.conversations.add(
    "PROJECT_ID",
    "CONVERSATION_ID",
  );
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### projects.conversations.create

Create an idle conversation within a project.

```typescript Signature theme={null}
(
  id: string,
  input?: { title?: string | null },
  options?: AgentResourceOptions,
) => Promise<AgentConversation>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.conversations.create(
    "PROJECT_ID",
    { title: "Product concepts" },
  );
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentConversation](types.md#agentconversation).

### projects.conversations.list

List visible conversations in a project. This method returns an array without a pagination cursor.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentProjectConversation[]>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.conversations.list(
    "PROJECT_ID",
  );
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentProjectConversation](types.md#agentprojectconversation).

### projects.conversations.remove

Remove a conversation's project association. This does not delete the conversation.

```typescript Signature theme={null}
(
  id: string,
  conversation: string,
  options?: AgentResourceOptions,
) => Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.conversations.remove(
    "PROJECT_ID",
    "CONVERSATION_ID",
  );
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### projects.conversations.setMemoryPrivacy

Set excluded to true to exclude the conversation from project memory. The conversation must belong to the project.

```typescript Signature theme={null}
(
  id: string,
  conversation: string,
  excluded: boolean,
  options?: AgentResourceOptions,
) => Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.conversations.setMemoryPrivacy(
    "PROJECT_ID",
    "CONVERSATION_ID",
    true,
  );
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### projects.create

Create a project with a name and optional color.

```typescript Signature theme={null}
(
  input: { name: string; color?: string },
  options?: AgentResourceOptions,
) => Promise<AgentProject>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.projects.create({
  name: "Autumn campaign",
});
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentProject](types.md#agentproject).

### projects.delete

Delete the project association and project-scoped resources. This does not delete its conversations or source media.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.delete("PROJECT_ID");
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### projects.documents.attach

Attach a fal storage URL and extracted text. Replace the sample file URL, metadata, and text with your uploaded document.

```typescript Signature theme={null}
(
  id: string,
  input: AgentProjectDocumentInput,
  options?: AgentResourceOptions,
) => Promise<AgentProjectDocument>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.documents.attach(
    "PROJECT_ID",
    {
      url: "https://fal.media/files/FILE_ID/brief.txt",
      fileName: "brief.txt",
      contentType: "text/plain",
      sizeBytes: 16,
      text: "Blue background.",
    },
  );
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentProjectDocumentInput](types.md#agentprojectdocumentinput), [AgentResourceOptions](types.md#agentresourceoptions), [AgentProjectDocument](types.md#agentprojectdocument).

### projects.documents.import

Extract and attach a document already uploaded to supported fal storage. The server downloads at most 25 MiB without following redirects. MIME type can be inferred from the filename. PDF and DOCX text is extracted automatically. Empty documents have no\_text status.

```typescript Signature theme={null}
(
  id: string,
  input: AgentProjectDocumentImport,
  options?: AgentResourceOptions,
) => Promise<AgentProjectDocument>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const document =
  await agent.projects.documents.import(
    "project_id",
    {
      url: "https://fal.media/files/brief.pdf",
      fileName: "brief.pdf",
    },
  );
console.log(document.status);
```

Guide: [projects](projects.md). Types: [AgentProjectDocumentImport](types.md#agentprojectdocumentimport), [AgentResourceOptions](types.md#agentresourceoptions), [AgentProjectDocument](types.md#agentprojectdocument).

### projects.documents.list

List project documents and their processing status.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentProjectDocument[]>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.documents.list("PROJECT_ID");
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentProjectDocument](types.md#agentprojectdocument).

### projects.documents.preview

Read a document's extracted text. Use the document's assetId.

```typescript Signature theme={null}
(
  id: string,
  assetId: string,
  options?: AgentResourceOptions,
) => Promise<{ text: string; truncated: boolean }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.documents.preview(
    "PROJECT_ID",
    "ASSET_ID",
  );
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### projects.documents.remove

Remove the document from the project. Use the document's assetId.

```typescript Signature theme={null}
(
  id: string,
  assetId: string,
  options?: AgentResourceOptions,
) => Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.documents.remove(
    "PROJECT_ID",
    "ASSET_ID",
  );
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### projects.documents.retry

Retry document processing. Read its status after submission.

```typescript Signature theme={null}
(
  id: string,
  assetId: string,
  options?: AgentResourceOptions,
) => Promise<AgentProjectDocument>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.projects.documents.retry(
  "PROJECT_ID",
  "ASSET_ID",
);
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentProjectDocument](types.md#agentprojectdocument).

### projects.documents.upload

Upload a document, extract its text, and attach it to a project. Supports PDF, DOCX, and text files up to 25 MiB. Ingestion continues in the background; poll documents.list for its status. Uploads use fal storage credentials configured on the client. Aborting observation does not undo an uploaded file.

```typescript Signature theme={null}
(
  id: string,
  file: File,
  options?: AgentResourceOptions,
) => Promise<AgentProjectDocument>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const file = new File(
  ["Use warm colors."],
  "brief.txt",
  {
    type: "text/plain",
  },
);
const document =
  await agent.projects.documents.upload(
    "project_id",
    file,
  );
console.log(document.status);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentProjectDocument](types.md#agentprojectdocument).

### projects.list

List project summaries, including counts and cover media.

```typescript Signature theme={null}
(options?: AgentResourceOptions) =>
  Promise<AgentProjectSummary[]>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.projects.list();
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentProjectSummary](types.md#agentprojectsummary).

### projects.memory.create

Create a memory note. supersededId identifies a note replaced by this operation, when present.

```typescript Signature theme={null}
(
  id: string,
  input: { kind: AgentMemoryKind; content: string },
  options?: AgentResourceOptions,
) =>
  Promise<{
    id: string;
    supersededId: string | null;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.projects.memory.create(
  "PROJECT_ID",
  {
    kind: "style",
    content: "Use a blue background.",
  },
);
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentMemoryKind](types.md#agentmemorykind), [AgentResourceOptions](types.md#agentresourceoptions).

### projects.memory.retrieve

Read the project's primer and active memory notes.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentProjectMemory>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.memory.retrieve(
    "PROJECT_ID",
  );
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentProjectMemory](types.md#agentprojectmemory).

### projects.memory.update

Edit, pin, delete, or restore a memory note within the project.

```typescript Signature theme={null}
(
  id: string,
  noteId: string,
  input: {
    content?: string;
    pinned?: boolean;
    status?: "active" | "deleted";
  },
  options?: AgentResourceOptions,
) => Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.projects.memory.update(
  "PROJECT_ID",
  "NOTE_ID",
  { pinned: true },
);
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### projects.resources

Read attached media, collections, characters, smart entities, and generated media.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentProjectResources>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.resources("PROJECT_ID");
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentProjectResources](types.md#agentprojectresources).

### projects.retrieve

Read project identity, name, color, and creation time.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentProject>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.projects.retrieve("PROJECT_ID");
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentProject](types.md#agentproject).

### projects.update

Change a project's name, color, or both. Supply at least one field.

```typescript Signature theme={null}
(
  id: string,
  input: { name?: string; color?: string },
  options?: AgentResourceOptions,
) => Promise<AgentProject>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.projects.update(
  "PROJECT_ID",
  { name: "Winter campaign" },
);
console.log(result);
```

Guide: [projects](projects.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentProject](types.md#agentproject).

## Models

### models.capabilities

Read a generation model's configurable fields, required flags, and default values.

```typescript Signature theme={null}
(
  endpointId: string,
  options?: AgentResourceOptions,
) => Promise<AgentModelCapabilities>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.models.capabilities(
  "MODEL_ENDPOINT_ID",
);
console.log(result);
```

Guide: [settings](settings.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentModelCapabilities](types.md#agentmodelcapabilities).

### models.list

Search generation models. Use page-based pagination, not a history cursor.

```typescript Signature theme={null}
(
  filter?: {
    keywords?: string;
    categories?: string[];
    page?: number;
    limit?: number;
  },
  options?: AgentResourceOptions,
) =>
  Promise<{
    items: AgentModel[];
    total?: number | null;
    page?: number;
    size?: number;
    pages?: number | null;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.models.list({
  keywords: "image",
  page: 1,
  limit: 10,
});
console.log(result);
```

Guide: [settings](settings.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentModel](types.md#agentmodel).

### models.listAgentModels

List available reasoning models. Use these IDs for the defaultModel account preference.

```typescript Signature theme={null}
(options?: AgentResourceOptions) =>
  Promise<
    {
      id: string;
      label: string;
      tag?: "recommended" | "experimental";
    }[]
  >;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.models.listAgentModels();
console.log(result);
```

Guide: [settings](settings.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

## Settings

### settings.conversations.retrieve

Read generation settings for a conversation.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentGenerationSettings>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const settings =
  await agent.settings.conversations.retrieve(
    "CONVERSATION_ID",
  );
console.log(settings);
```

Guide: [settings](settings.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentGenerationSettings](types.md#agentgenerationsettings).

### settings.conversations.update

Replace conversation settings with the expected revision. Preserve unrelated fields from the latest read.

```typescript Signature theme={null}
(
  id: string,
  input: {
    settings: AgentGenerationSettings;
    expectedRevision: number;
  },
  options?: AgentResourceOptions,
) => Promise<AgentGenerationSettings>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const settings =
  await agent.settings.conversations.retrieve(
    "CONVERSATION_ID",
  );
const result =
  await agent.settings.conversations.update(
    "CONVERSATION_ID",
    {
      settings: {
        ...settings,
        reviewBeforeGenerating: true,
      },
      expectedRevision: settings.revision,
    },
  );
console.log(result);
```

Guide: [settings](settings.md). Types: [AgentGenerationSettings](types.md#agentgenerationsettings), [AgentResourceOptions](types.md#agentresourceoptions).

### settings.defaults.retrieve

Read local, inherited, and effective generation defaults with their sources.

```typescript Signature theme={null}
(
  target: AgentDefaultsTarget,
  options?: AgentResourceOptions,
) => Promise<AgentDefaultsView>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.settings.defaults.retrieve({
    scope: "personal",
  });
console.log(result);
```

Guide: [settings](settings.md). Types: [AgentDefaultsTarget](types.md#agentdefaultstarget), [AgentResourceOptions](types.md#agentresourceoptions), [AgentDefaultsView](types.md#agentdefaultsview).

### settings.defaults.update

Change or inherit defaults. Send the last local object as expectedLocal to detect conflicting edits.

```typescript Signature theme={null}
(
  target: AgentDefaultsTarget,
  input: AgentDefaultsUpdate,
  options?: AgentResourceOptions,
) => Promise<AgentDefaultsView>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const target = { scope: "personal" } as const;
const defaults =
  await agent.settings.defaults.retrieve(target);
const result = await agent.settings.defaults.update(
  target,
  {
    expectedLocal: defaults.local,
    changes: {
      preferences: { aspect_ratio: "16:9" },
      preferredModels: {},
    },
  },
);
console.log(result.effective);
```

Guide: [settings](settings.md). Types: [AgentDefaultsTarget](types.md#agentdefaultstarget), [AgentDefaultsUpdate](types.md#agentdefaultsupdate), [AgentResourceOptions](types.md#agentresourceoptions), [AgentDefaultsView](types.md#agentdefaultsview).

### settings.projects.retrieve

Read generation settings saved on a project.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentGenerationSettings>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const settings =
  await agent.settings.projects.retrieve(
    "PROJECT_ID",
  );
console.log(settings);
```

Guide: [settings](settings.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentGenerationSettings](types.md#agentgenerationsettings).

### settings.projects.update

Replace project settings with the expected revision. Preserve unrelated fields from the latest read.

```typescript Signature theme={null}
(
  id: string,
  input: {
    settings: AgentGenerationSettings;
    expectedRevision: number;
  },
  options?: AgentResourceOptions,
) => Promise<AgentGenerationSettings>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const settings =
  await agent.settings.projects.retrieve(
    "PROJECT_ID",
  );
const result = await agent.settings.projects.update(
  "PROJECT_ID",
  {
    settings: {
      ...settings,
      reviewBeforeGenerating: true,
    },
    expectedRevision: settings.revision,
  },
);
console.log(result);
```

Guide: [settings](settings.md). Types: [AgentGenerationSettings](types.md#agentgenerationsettings), [AgentResourceOptions](types.md#agentresourceoptions).

## Preferences

### preferences.retrieve

Read account preferences, including generation defaults.

```typescript Signature theme={null}
(options?: AgentResourceOptions) =>
  Promise<AgentPreferences>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.preferences.retrieve();
console.log(result);
```

Guide: [settings](settings.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentPreferences](types.md#agentpreferences).

### preferences.update

Update fields within one preference section. The section determines the accepted input fields.

```typescript Signature theme={null}
<
  S extends
    | "general"
    | "cost"
    | "skills"
    | "notifications",
>(
  section: S,
  input: Partial<AgentPreferences[S]>,
  options?: AgentResourceOptions,
) => Promise<AgentPreferences>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.preferences.update(
  "notifications",
  { turnComplete: false },
);
console.log(result);
```

Guide: [settings](settings.md). Types: [AgentPreferences](types.md#agentpreferences), [AgentResourceOptions](types.md#agentresourceoptions).

## Queue

### queue.cancel

Cancel a queued turn by its turn ID.

```typescript Signature theme={null}
(
  conversation: string,
  turnId: string,
  options?: AgentResourceOptions,
) => Promise<{ success: boolean }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.queue.cancel(
  "CONVERSATION_ID",
  "TURN_ID",
);
console.log(result);
```

Guide: [queue](queue.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### queue.dispatch

Attempt to dispatch eligible queued work. promoted false means this call did not start a turn.

```typescript Signature theme={null}
(
  conversation: string,
  options?: AgentResourceOptions,
) => Promise<AgentQueueDispatch>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.queue.dispatch(
  "CONVERSATION_ID",
);
console.log(result);
```

Guide: [queue](queue.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentQueueDispatch](types.md#agentqueuedispatch).

### queue.edit

Replace a queued prompt with 1–10,000 characters. This does not edit a running response.

```typescript Signature theme={null}
(
  conversation: string,
  turnId: string,
  content: string,
  options?: AgentResourceOptions,
) => Promise<{ success: boolean }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.queue.edit(
  "CONVERSATION_ID",
  "TURN_ID",
  "Use a blue background.",
);
console.log(result);
```

Guide: [queue](queue.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### queue.reorder

Reorder queued turns by turn ID. Supply at most 50 IDs. Steps within a plan retain their required order. Retrieve the queue for its effective order.

```typescript Signature theme={null}
(
  conversation: string,
  turnIds: string[],
  options?: AgentResourceOptions,
) => Promise<{ success: boolean }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.queue.reorder(
  "CONVERSATION_ID",
  ["TURN_ID_1", "TURN_ID_2"],
);
console.log(result);
```

Guide: [queue](queue.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### queue.retrieve

Read queued turns and active turn IDs for a conversation.

```typescript Signature theme={null}
(
  conversation: string,
  options?: AgentResourceOptions,
) =>
  Promise<{
    items: AgentQueueItem[];
    active_turn_ids: string[];
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.queue.retrieve(
  "CONVERSATION_ID",
);
console.log(result);
```

Guide: [queue](queue.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentQueueItem](types.md#agentqueueitem).

### queue.run

Request immediate execution of a queued turn. This can bypass its normal queue position.

```typescript Signature theme={null}
(
  conversation: string,
  turnId: string,
  options?: AgentResourceOptions,
) => Promise<AgentQueueDispatch>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.queue.run(
  "CONVERSATION_ID",
  "TURN_ID",
);
console.log(result);
```

Guide: [queue](queue.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentQueueDispatch](types.md#agentqueuedispatch).

### queue.setApproval

Change a continuation's approval requirement. Removing the requirement can dispatch work immediately.

```typescript Signature theme={null}
(
  conversation: string,
  turnId: string,
  input: {
    requiresApproval: boolean;
    approveCheckpoints?: boolean;
  },
  options?: AgentResourceOptions,
) =>
  Promise<{
    updated: boolean;
    dispatch: AgentQueueDispatch | null;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.queue.setApproval(
  "CONVERSATION_ID",
  "TURN_ID",
  { requiresApproval: true },
);
console.log(result);
```

Guide: [queue](queue.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentQueueDispatch](types.md#agentqueuedispatch).

### queue.setHalted

Pause or resume queue dispatch. Resuming also attempts to dispatch eligible work.

```typescript Signature theme={null}
(
  conversation: string,
  halted: boolean,
  options?: AgentResourceOptions,
) =>
  Promise<{
    updatedCount: number;
    dispatch: AgentQueueDispatch | null;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.queue.setHalted(
  "CONVERSATION_ID",
  true,
);
console.log(result);
```

Guide: [queue](queue.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentQueueDispatch](types.md#agentqueuedispatch).

## Runs

### runs.answer

Answer a cost approval from the retrieved run. The decision applies to its approval group.

```typescript Signature theme={null}
(
  id: string,
  conversation: string,
  input: {
    input_request_id: string;
    decision: "approve" | "reject";
  },
  options?: AgentResourceOptions,
) =>
  Promise<{
    submittedCount?: number;
    failedCount?: number;
    cancelledCount?: number;
  }>;
```

Example:

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
console.log(result);
```

Guide: [queue](queue.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### runs.cancel

Request cancellation of a generation run. Inspect cancelled and retrieve the run afterward.

```typescript Signature theme={null}
(
  id: string,
  conversation: string,
  options?: AgentResourceOptions,
) => Promise<{ cancelled: boolean }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.runs.cancel(
  "RUN_ID",
  "CONVERSATION_ID",
);
console.log(result);
```

Guide: [queue](queue.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### runs.retrieve

Read a generation run's operation, artifacts, and pending cost approvals.

```typescript Signature theme={null}
(
  id: string,
  conversation: string,
  options?: AgentResourceOptions,
) => Promise<AgentRunView>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.runs.retrieve(
  "RUN_ID",
  "CONVERSATION_ID",
);
console.log(result);
```

Guide: [queue](queue.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentRunView](types.md#agentrunview).

### runs.retry

Request another generation attempt. This can incur charges and require a new approval.

```typescript Signature theme={null}
(
  id: string,
  conversation: string,
  options?: AgentResourceOptions,
) =>
  Promise<{
    mediaId: string;
    runId: string;
    attempt: number;
    requestId: string;
    approvalRequired?: boolean;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.runs.retry(
  "RUN_ID",
  "CONVERSATION_ID",
);
console.log(result);
```

Guide: [queue](queue.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

## Library

### library.assets.assignTag

Assign an existing tag to an asset record.

```typescript Signature theme={null}
(
  id: string,
  tagId: string,
  options?: AgentResourceOptions,
) => Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

await agent.library.assets.assignTag(
  "ASSET_RECORD_ID",
  "TAG_ID",
);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### library.assets.delete

Delete a library asset using its assetRecordId.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.library.assets.delete(
  "ASSET_RECORD_ID",
);
console.log(result);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### library.assets.list

Search library assets. Follow nextCursor and inspect scopeTruncated before treating the result as complete.

```typescript Signature theme={null}
(
  input?: AgentLibraryAssetQuery,
  options?: AgentResourceOptions,
) =>
  Promise<{
    items: AgentLibraryAsset[];
    nextCursor: string | null;
    totalCount: number | null;
    scopeTruncated: boolean;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.library.assets.list({
  mediaTypes: ["image"],
  limit: 20,
});
console.log(result);
```

Guide: [library](library.md). Types: [AgentLibraryAssetQuery](types.md#agentlibraryassetquery), [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryAsset](types.md#agentlibraryasset).

### library.assets.register

Register a media URL returned by fal storage. Replace the sample URL with your uploaded media URL.

```typescript Signature theme={null}
(
  input: {
    url: string;
    type: AgentLibraryMediaType;
    size?: number;
    collectionId?: string | null;
    favorite?: boolean;
  },
  options?: AgentResourceOptions,
) => Promise<AgentLibraryAsset>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.library.assets.register({
  url: "https://fal.media/files/FILE_ID/mug.png",
  type: "image",
});
console.log(result);
```

Guide: [library](library.md). Types: [AgentLibraryMediaType](types.md#agentlibrarymediatype), [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryAsset](types.md#agentlibraryasset).

### library.assets.removeTag

Remove a tag assignment without deleting the tag or asset.

```typescript Signature theme={null}
(
  id: string,
  tagId: string,
  options?: AgentResourceOptions,
) => Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

await agent.library.assets.removeTag(
  "ASSET_RECORD_ID",
  "TAG_ID",
);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### library.assets.retrieve

Read asset metadata using its library assetRecordId.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentLibraryAsset>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.library.assets.retrieve(
  "ASSET_RECORD_ID",
);
console.log(result);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryAsset](types.md#agentlibraryasset).

### library.assets.setFavorite

Set an asset's favorite state using its assetRecordId.

```typescript Signature theme={null}
(
  id: string,
  favorite: boolean,
  options?: AgentResourceOptions,
) =>
  Promise<{
    assetRecordId: string;
    assetId: null;
    isFavorited: boolean;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.library.assets.setFavorite(
    "ASSET_RECORD_ID",
    true,
  );
console.log(result);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### library.assets.tags

Read the tags assigned to an asset record.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentLibraryTag[]>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const tags = await agent.library.assets.tags(
  "ASSET_RECORD_ID",
);
console.log(tags);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryTag](types.md#agentlibrarytag).

### library.assets.updatePrompt

Update an uploaded asset's prompt with 1–2,000 characters. Generated assets do not support this operation.

```typescript Signature theme={null}
(
  id: string,
  prompt: string,
  options?: AgentResourceOptions,
) => Promise<AgentLibraryAsset>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.library.assets.updatePrompt(
    "ASSET_RECORD_ID",
    "Blue ceramic mug",
  );
console.log(result);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryAsset](types.md#agentlibraryasset).

### library.characters.checkIdentifier

Normalize a proposed character identifier and check availability. Creation can still fail if another request takes it first.

```typescript Signature theme={null}
(
  identifier: string,
  options?: AgentResourceOptions,
) =>
  Promise<{
    identifier: string;
    available: boolean;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.library.characters.checkIdentifier(
    "milo",
  );
console.log(result);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### library.characters.create

Create a character from a description and one to twenty reference images. References accept fal-hosted URLs or existing asset targets.

```typescript Signature theme={null}
(
  input: AgentCharacterInput & {
    identifier?: string;
  },
  options?: AgentResourceOptions,
) =>
  Promise<
    AgentLibraryCollection & { type: "character" }
  >;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const character =
  await agent.library.characters.create({
    name: "Milo",
    identifier: "milo",
    description: "A gray cat with green eyes.",
    referenceImages: [
      "https://fal.media/files/REFERENCE.png",
    ],
  });
console.log(character);
```

Guide: [library](library.md). Types: [AgentCharacterInput](types.md#agentcharacterinput), [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryCollection](types.md#agentlibrarycollection).

### library.characters.references

Read reference image URLs and asset record IDs. Exclude entries with isCover when saving the reference list.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentCharacterReference[]>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const references =
  await agent.library.characters.references(
    "CHARACTER_ID",
  );
console.log(references);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentCharacterReference](types.md#agentcharacterreference).

### library.characters.update

Replace a character name, description, and complete reference list. Character identifiers cannot be changed.

```typescript Signature theme={null}
(
  id: string,
  input: AgentCharacterInput,
  options?: AgentResourceOptions,
) =>
  Promise<
    AgentLibraryCollection & { type: "character" }
  >;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const character =
  await agent.library.characters.update(
    "CHARACTER_ID",
    {
      name: "Milo",
      description: "A gray cat with green eyes.",
      referenceImages: [
        "https://fal.media/files/REFERENCE.png",
      ],
    },
  );
console.log(character);
```

Guide: [library](library.md). Types: [AgentCharacterInput](types.md#agentcharacterinput), [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryCollection](types.md#agentlibrarycollection).

### library.collections.addAsset

Add an asset to a manual collection or smart entity gallery. Use the assetRecordId. This does not change defining references.

```typescript Signature theme={null}
(
  id: string,
  assetRecordId: string,
  options?: AgentResourceOptions,
) =>
  Promise<{
    collectionId: string;
    assetRecordId: string;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.library.collections.addAsset(
    "COLLECTION_ID",
    "ASSET_RECORD_ID",
  );
console.log(result);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### library.collections.create

Create a manual collection or a smart collection with filters.

```typescript Signature theme={null}
(
  input: AgentCollectionInput,
  options?: AgentResourceOptions,
) => Promise<AgentLibraryCollection>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.library.collections.create({
    name: "Mugs",
  });
console.log(result);
```

Guide: [library](library.md). Types: [AgentCollectionInput](types.md#agentcollectioninput), [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryCollection](types.md#agentlibrarycollection).

### library.collections.delete

Delete a collection or smart entity through the shared product operation. This does not delete its source assets.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.library.collections.delete(
    "COLLECTION_ID",
  );
console.log(result);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### library.collections.list

List collections with offset pagination. includeCharacters preserves character inclusion. Set includeSmartEntities to true to include all five entity types.

```typescript Signature theme={null}
(
  input?: {
    limit?: number;
    offset?: number;
    includeCharacters?: boolean;
    includeSmartEntities?: boolean;
  },
  options?: AgentResourceOptions,
) => Promise<AgentLibraryCollection[]>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.library.collections.list(
  {
    limit: 20,
    offset: 0,
    includeSmartEntities: true,
  },
);
console.log(result);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryCollection](types.md#agentlibrarycollection).

### library.collections.move

Move a collection under a parent. Use null to move it to the root.

```typescript Signature theme={null}
(
  id: string,
  parentCollectionId: string | null,
  options?: AgentResourceOptions,
) => Promise<AgentLibraryCollection>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result = await agent.library.collections.move(
  "COLLECTION_ID",
  null,
);
console.log(result);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryCollection](types.md#agentlibrarycollection).

### library.collections.removeAsset

Remove a manual collection membership or smart entity gallery link. Assets, defining references, and recorded generation usage are preserved.

```typescript Signature theme={null}
(
  id: string,
  assetRecordId: string,
  options?: AgentResourceOptions,
) => Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.library.collections.removeAsset(
    "COLLECTION_ID",
    "ASSET_RECORD_ID",
  );
console.log(result);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### library.collections.setFavorite

Set a collection or smart entity's favorite state.

```typescript Signature theme={null}
(
  id: string,
  favorite: boolean,
  options?: AgentResourceOptions,
) => Promise<AgentLibraryCollection>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.library.collections.setFavorite(
    "COLLECTION_ID",
    true,
  );
console.log(result);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryCollection](types.md#agentlibrarycollection).

### library.collections.update

Update collection metadata or filters. Use null to clear nullable fields.

```typescript Signature theme={null}
(
  id: string,
  input: {
    name?: string;
    description?: string | null;
    icon?: string | null;
    color?: string | null;
    coverImageUrl?: string | null;
    filters?: AgentCollectionFilter | null;
  },
  options?: AgentResourceOptions,
) => Promise<AgentLibraryCollection>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.library.collections.update(
    "COLLECTION_ID",
    { description: "Ceramic mug concepts" },
  );
console.log(result);
```

Guide: [library](library.md). Types: [AgentCollectionFilter](types.md#agentcollectionfilter), [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryCollection](types.md#agentlibrarycollection).

### library.entities.addAsset

Link an existing asset to an entity gallery. This does not change its defining reference images.

```typescript Signature theme={null}
(
  id: string,
  assetRef: string,
  options?: AgentResourceOptions,
) =>
  Promise<{
    smartEntityId: string;
    assetRecordId: string;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

await agent.library.entities.addAsset(
  "ENTITY_ID",
  "ASSET_RECORD_ID",
);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### library.entities.checkHandle

Normalize a handle and check availability across all five types. Set excludeId when editing. Availability does not reserve the handle.

```typescript Signature theme={null}
(
  input: { handle: string; excludeId?: string },
  options?: AgentResourceOptions,
) =>
  Promise<{
    handle: string;
    available: boolean;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const result =
  await agent.library.entities.checkHandle({
    handle: "blue-mug",
    excludeId: "PROP_ID",
  });
console.log(result.handle, result.available);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### library.entities.create

Create any smart entity type with one to twenty defining references. Characters require a description and reject custom metadata.

```typescript Signature theme={null}
(
  input: AgentLibraryEntityInput,
  options?: AgentResourceOptions,
) => Promise<AgentLibraryEntity>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const entity = await agent.library.entities.create({
  type: "prop",
  name: "Blue mug",
  handle: "blue-mug",
  referenceImages: [
    "https://fal.media/files/MUG.png",
  ],
});
console.log(entity);
```

Guide: [library](library.md). Types: [AgentLibraryEntityInput](types.md#agentlibraryentityinput), [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryEntity](types.md#agentlibraryentity).

### library.entities.delete

Delete any smart entity type through the shared product operation. Source assets are preserved.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

await agent.library.entities.delete("ENTITY_ID");
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### library.entities.list

List smart entities with offset pagination. Filter by types or search names and handles. Omit types to include all five entity types.

```typescript Signature theme={null}
(
  input?: AgentLibraryEntityQuery,
  options?: AgentResourceOptions,
) => Promise<AgentLibraryEntity[]>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const entities = await agent.library.entities.list({
  types: [
    "character",
    "prop",
    "environment",
    "style",
    "scene",
  ],
  limit: 100,
  offset: 0,
});
console.log(entities);
```

Guide: [library](library.md). Types: [AgentLibraryEntityQuery](types.md#agentlibraryentityquery), [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryEntity](types.md#agentlibraryentity).

### library.entities.listAssets

Read associated media with offset pagination. Set includeReferences to include images whose only association is defining reference membership.

```typescript Signature theme={null}
(
  id: string,
  input?: {
    mediaTypes?: AgentLibraryMediaType[];
    limit?: number;
    offset?: number;
    includeReferences?: boolean;
  },
  options?: AgentResourceOptions,
) =>
  Promise<{
    items: AgentLibraryAsset[];
    nextOffset: number | null;
    totalCount: number;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const page =
  await agent.library.entities.listAssets(
    "ENTITY_ID",
    { mediaTypes: ["image"], limit: 50, offset: 0 },
  );
console.log(
  page.items,
  page.nextOffset,
  page.totalCount,
);
```

Guide: [library](library.md). Types: [AgentLibraryMediaType](types.md#agentlibrarymediatype), [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryAsset](types.md#agentlibraryasset).

### library.entities.removeAsset

Remove a manual gallery link without deleting the asset. Recorded generation usage and defining reference images are preserved.

```typescript Signature theme={null}
(
  id: string,
  assetRef: string,
  options?: AgentResourceOptions,
) => Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

await agent.library.entities.removeAsset(
  "ENTITY_ID",
  "ASSET_RECORD_ID",
);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### library.entities.resolve

Resolve one to 100 handles within the account. Unknown handles are omitted. Use types to restrict the matching entity types.

```typescript Signature theme={null}
(
  input: {
    handles: string[];
    types?: AgentLibraryEntityType[];
  },
  options?: AgentResourceOptions,
) => Promise<AgentLibraryEntity[]>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const entities =
  await agent.library.entities.resolve({
    handles: [
      "@milo",
      "@blue-mug",
      "@workshop",
      "@watercolor",
      "@breakfast",
    ],
  });
console.log(entities);
```

Guide: [library](library.md). Types: [AgentLibraryEntityType](types.md#agentlibraryentitytype), [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryEntity](types.md#agentlibraryentity).

### library.entities.retrieve

Read an entity and its defining reference-image URLs. The references exclude its associated-media gallery.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentLibraryEntity>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const entity =
  await agent.library.entities.retrieve(
    "ENTITY_ID",
  );
console.log(
  entity.type,
  entity.metadata,
  entity.references,
);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryEntity](types.md#agentlibraryentity).

### library.entities.setFavorite

Set the favorite state for any smart entity type.

```typescript Signature theme={null}
(
  id: string,
  favorite: boolean,
  options?: AgentResourceOptions,
) => Promise<AgentLibraryEntity>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const entity =
  await agent.library.entities.setFavorite(
    "ENTITY_ID",
    true,
  );
console.log(entity);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryEntity](types.md#agentlibraryentity).

### library.entities.update

Update entity fields. Supplied references replace the complete defining set. Types and character handles cannot change. Character metadata is managed automatically.

```typescript Signature theme={null}
(
  id: string,
  input: AgentLibraryEntityUpdate,
  options?: AgentResourceOptions,
) => Promise<AgentLibraryEntity>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const entity = await agent.library.entities.update(
  "PROP_ID",
  {
    description:
      "A blue ceramic mug with a rounded handle.",
    referenceImages: [
      "https://fal.media/files/MUG_FRONT.png",
      "https://fal.media/files/MUG_SIDE.png",
    ],
  },
);
console.log(entity);
```

Guide: [library](library.md). Types: [AgentLibraryEntityUpdate](types.md#agentlibraryentityupdate), [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryEntity](types.md#agentlibraryentity).

### library.tags.create

Create a tag. Names are trimmed and stored in lowercase.

```typescript Signature theme={null}
(
  input: { name: string; color?: string },
  options?: AgentResourceOptions,
) => Promise<AgentLibraryTag>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const tag = await agent.library.tags.create({
  name: "campaign",
  color: "blue",
});
console.log(tag);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryTag](types.md#agentlibrarytag).

### library.tags.delete

Delete a tag and its asset assignments. This does not delete assets.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<{ success: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

await agent.library.tags.delete("TAG_ID");
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### library.tags.list

List available asset tags. System tags are excluded.

```typescript Signature theme={null}
(options?: AgentResourceOptions) =>
  Promise<AgentLibraryTag[]>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const tags = await agent.library.tags.list();
console.log(tags);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryTag](types.md#agentlibrarytag).

### library.tags.update

Change a tag name or color. Tag names must be unique within the account.

```typescript Signature theme={null}
(
  id: string,
  input: { name?: string; color?: string },
  options?: AgentResourceOptions,
) => Promise<AgentLibraryTag>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const tag = await agent.library.tags.update(
  "TAG_ID",
  { name: "launch" },
);
console.log(tag);
```

Guide: [library](library.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentLibraryTag](types.md#agentlibrarytag).

## Skills

### skills.checkForUpdates

Check an imported skill against its repository default branch. Custom and detached skills cannot check for updates.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<{
    currentSha: string | null;
    latestSha: string;
    hasUpdate: boolean;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const update =
  await agent.skills.checkForUpdates("SKILL_ID");
console.log(update.hasUpdate);
```

Guide: [skills](skills.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### skills.create

Create a custom skill. Names use lowercase letters, digits, and hyphens. presetClash reports a matching fal skill.

```typescript Signature theme={null}
(
  input: AgentSkillContent,
  options?: AgentResourceOptions,
) =>
  Promise<{
    skill: AgentInstalledSkill;
    presetClash: boolean;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const { skill } = await agent.skills.create({
  name: "product-photos",
  description: "Create consistent product photos.",
  body: "Preserve the product shape and colors.",
});
console.log(skill.id);
```

Guide: [skills](skills.md). Types: [AgentSkillContent](types.md#agentskillcontent), [AgentResourceOptions](types.md#agentresourceoptions), [AgentInstalledSkill](types.md#agentinstalledskill).

### skills.importFromGithub

Import a skill from a public GitHub repository. Script-based skills are unsupported.

```typescript Signature theme={null}
(
  input: AgentSkillSource,
  options?: AgentResourceOptions,
) => Promise<AgentInstalledSkill>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const skill = await agent.skills.importFromGithub({
  repoUrl: "https://github.com/OWNER/REPO",
  subpath: "skills/photo-editing",
});
console.log(skill.id);
```

Guide: [skills](skills.md). Types: [AgentSkillSource](types.md#agentskillsource), [AgentResourceOptions](types.md#agentresourceoptions), [AgentInstalledSkill](types.md#agentinstalledskill).

### skills.list

List fal skills and your installed skills. Disabled and shadowed flags explain which skills are available.

```typescript Signature theme={null}
(
  filter?: {
    search?: string;
    conversationId?: string;
  },
  options?: AgentResourceOptions,
) => Promise<AgentSkill[]>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const skills = await agent.skills.list();
console.log(skills);
```

Guide: [skills](skills.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentSkill](types.md#agentskill).

### skills.previewImport

Inspect a public GitHub skill before importing it. Check scripts and name conflicts before continuing.

```typescript Signature theme={null}
(
  input: AgentSkillSource,
  options?: AgentResourceOptions,
) => Promise<AgentSkillImportPreview>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const preview = await agent.skills.previewImport({
  repoUrl: "https://github.com/OWNER/REPO",
  subpath: "skills/photo-editing",
});
console.log(preview);
```

Guide: [skills](skills.md). Types: [AgentSkillSource](types.md#agentskillsource), [AgentResourceOptions](types.md#agentresourceoptions), [AgentSkillImportPreview](types.md#agentskillimportpreview).

### skills.retrieve

Read a visible fal or installed skill by ID.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentSkill>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const skill =
  await agent.skills.retrieve("SKILL_ID");
console.log(skill.body);
```

Guide: [skills](skills.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentSkill](types.md#agentskill).

### skills.save

Replace custom skill content. Saving an imported skill detaches its GitHub source and disables source updates.

```typescript Signature theme={null}
(
  id: string,
  content: AgentSkillContent,
  options?: AgentResourceOptions,
) =>
  Promise<{
    skill: AgentInstalledSkill;
    presetClash: boolean;
  }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

await agent.skills.save("SKILL_ID", {
  name: "product-photos",
  description: "Create consistent product photos.",
  body: "Preserve the product shape and colors.",
});
```

Guide: [skills](skills.md). Types: [AgentSkillContent](types.md#agentskillcontent), [AgentResourceOptions](types.md#agentresourceoptions), [AgentInstalledSkill](types.md#agentinstalledskill).

### skills.setEnabled

Enable or disable one skill. Pass its origin and ID from skills.list. The account-level skills switch still applies.

```typescript Signature theme={null}
(
  input: {
    origin: "fal" | "user";
    key: string;
    enabled: boolean;
  },
  options?: AgentResourceOptions,
) => Promise<{ ok: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

await agent.skills.setEnabled({
  origin: "user",
  key: "SKILL_ID",
  enabled: true,
});
```

Guide: [skills](skills.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### skills.uninstall

Remove an installed user skill and its disabled preference entry. Fal skills cannot be uninstalled.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<{ ok: true }>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

await agent.skills.uninstall("SKILL_ID");
```

Guide: [skills](skills.md). Types: [AgentResourceOptions](types.md#agentresourceoptions).

### skills.update

Update an imported skill from its repository default branch. Replaces its body, references, assets, and source commit.

```typescript Signature theme={null}
(id: string, options?: AgentResourceOptions) =>
  Promise<AgentInstalledSkill>;
```

Example:

```ts theme={null}
import { agent } from "./client.ts";

const skill = await agent.skills.update("SKILL_ID");
console.log(skill.sourceCommitSha);
```

Guide: [skills](skills.md). Types: [AgentResourceOptions](types.md#agentresourceoptions), [AgentInstalledSkill](types.md#agentinstalledskill).
