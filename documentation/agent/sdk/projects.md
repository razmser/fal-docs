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

# Projects, documents, and memory

> Organize conversations and media, attach documents, and maintain project memory.

Use the [quickstart client](quickstart.md#create-a-client). Replace uppercase IDs with your resource IDs.

## Create and manage projects

```ts theme={null}
import { agent } from "./client.ts";

const project = await agent.projects.create({
  name: "Autumn campaign",
  color: "#2563eb",
});
const conversation =
  await agent.projects.conversations.create(
    project.id,
    {
      title: "Product concepts",
    },
  );

console.log(project.id, conversation.id);
```

Project names contain 1–40 characters after trimming.
Colors accept preset tag colors or valid hexadecimal colors, with a maximum of 20 characters.

| Method | Behavior |
| - | - |
| `projects.list()` | Return project summaries as an array. |
| `projects.retrieve(projectId)` | Read the project's ID, name, color, and creation time. |
| `projects.update(projectId, { name?, color? })` | Change at least one field. |
| `projects.delete(projectId)` | Delete the project and its memory, document records, and saved settings. |

Deleting a project does not delete its conversations or source media.
Project summaries include counts, update times, and cover media.
See [AgentProjectSummary](types.md#agentprojectsummary) for every field.

## Manage conversations

| Method | Behavior |
| - | - |
| `projects.conversations.list(projectId)` | List visible conversations with their title, media count, and update time. |
| `projects.conversations.create(projectId, { title }?)` | Create an idle conversation. |
| `projects.conversations.add(projectId, conversationId)` | Add an existing conversation. |
| `projects.conversations.remove(projectId, conversationId)` | Remove its project association without deleting the conversation. |
| `projects.conversations.setMemoryPrivacy(projectId, conversationId, excluded)` | Control whether the conversation contributes to project memory. |

```ts theme={null}
import { agent } from "./client.ts";

await agent.projects.conversations.add(
  "PROJECT_ID",
  "CONVERSATION_ID",
);
await agent.projects.conversations.setMemoryPrivacy(
  "PROJECT_ID",
  "CONVERSATION_ID",
  true,
);
```

The privacy change requires membership in the specified project.
An exclusion controls memory participation. It does not delete the conversation.

## Attach media and collections

```ts theme={null}
import { agent } from "./client.ts";

await agent.projects.assets.attach(
  "PROJECT_ID",
  "ASSET_ID",
);
await agent.projects.collections.attach(
  "PROJECT_ID",
  "COLLECTION_ID",
);

const resources =
  await agent.projects.resources("PROJECT_ID");
console.log(
  resources.attachedMedia,
  resources.collections,
);
```

Project attachments accept an asset's `assetId` or its library `assetRecordId`. Library mutations use `assetRecordId`.
SDK artifact IDs cannot identify project attachments or library assets.

Use `projects.assets.detach(projectId, assetId)` or `projects.collections.detach(projectId, collectionId)` to remove an association.
Detaching does not delete the source asset or collection.

The resources response contains `attachedMedia`, `collections`, `characters`, `smartEntities`, and `generatedMedia`.
Attached media has typed identity and display fields.
The other arrays contain JSON records with product-specific fields.
Check each record before reading fields outside the [declared type](types.md#agentprojectresources).

## Attach a document

Upload and attach a file in one call. The server extracts PDF, DOCX, and text content.
This example uses the global `File` constructor available in the [quickstart's Node.js environment](quickstart.md).

```ts theme={null}
import { agent } from "./client.ts";

const file = new File(
  ["Use warm product lighting."],
  "brief.txt",
  {
    type: "text/plain",
  },
);
const document =
  await agent.projects.documents.upload(
    "PROJECT_ID",
    file,
  );
console.log(document.assetId, document.status);
```

Uploads use the client's fal storage credentials. Document ingestion continues in the background.
Use `documents.list(projectId)` to check its status.

To attach a file already in fal storage, use `documents.import`:

```ts theme={null}
import { agent } from "./client.ts";

const document =
  await agent.projects.documents.import(
    "PROJECT_ID",
    {
      url: "https://fal.media/files/BRIEF.pdf",
      fileName: "brief.pdf",
    },
  );
console.log(document.status);
```

The server accepts supported fal storage URLs, rejects redirects, and limits downloads to 25 MiB and 30 seconds.
DOCX archives also have a 25 MiB expanded limit and a maximum of 1,000 entries.
Automatic extraction keeps up to 250,000 characters, plus a truncation notice. It does not perform OCR.
Request timeout and cancellation cover both the storage upload and document import.
A failed attachment can leave the file in storage. Use `documents.import` to retry with its URL when available.

### Supply extracted text

Use `documents.attach(projectId, input)` when you already have extracted text, including text from your own OCR process.

| Input | Meaning |
| - | - |
| `url` | HTTPS URL returned by fal storage. Arbitrary external hosts are rejected. |
| `fileName` | Original document name. |
| `contentType` | Document MIME type. |
| `sizeBytes` | Original file size in bytes, not the extracted text length. |
| `text` | Extracted document text. |
| `pageCount` | Optional page count, or `null`. |
| `truncated` | Whether extraction omitted part of the document. |

A project supports up to 50 documents. Each file can be at most 25 MiB.
The text can contain at most 260,000 characters. The filename contains 1–256 characters and cannot contain a null character.
`pageCount` is a nonnegative integer when supplied.

Accepted MIME types are `application/pdf`, `application/vnd.openxmlformats-officedocument.wordprocessingml.document`, `text/plain`, `text/markdown`, `text/csv`, `text/tab-separated-values`, and `application/json`.

Use `assetId` for subsequent document operations.

```ts theme={null}
import { agent } from "./client.ts";

const documents =
  await agent.projects.documents.list("PROJECT_ID");
console.log(documents);

const preview =
  await agent.projects.documents.preview(
    "PROJECT_ID",
    "ASSET_ID",
  );
console.log(preview.text, preview.truncated);
```

Check `status` and `error` in subsequent list results until processing completes.
Document states include `pending`, `processing`, `ready`, `failed`, and `no_text`. Treat unrecognized states as nonfinal.
A `no_text` document has no usable extracted text. Supply extracted text from a supported extraction method.
Use `projects.documents.retry(projectId, assetId)` to retry failed processing.
Use `projects.documents.remove(projectId, assetId)` to remove the document.

## Maintain project memory

```ts theme={null}
import { agent } from "./client.ts";

const note = await agent.projects.memory.create(
  "PROJECT_ID",
  {
    kind: "style",
    content:
      "Use warm lighting and a blue background.",
  },
);
await agent.projects.memory.update(
  "PROJECT_ID",
  note.id,
  { pinned: true },
);
```

A note's `kind` is `decision`, `fact`, `preference`, `style`, or `entity`.
Its content contains 1–500 characters after trimming.
A create result contains `id` and `supersededId`.
A non-null `supersededId` identifies the note that the new note replaced.

`projects.memory.retrieve(projectId)` returns the `primer`, `primerUpdatedAt`, and active `notes`.
Each note contains its ID, kind, content, pinned state, source conversation ID, and creation time.
A missing primer, source conversation, or note kind can be `null`.

```ts theme={null}
import { agent } from "./client.ts";

await agent.projects.memory.update(
  "PROJECT_ID",
  "NOTE_ID",
  {
    content:
      "Use soft daylight and a blue background.",
  },
);
```

Set `status: "deleted"` to delete a note. Set `status: "active"` to restore it.
Keep its ID if your application supports undo.

## Handle errors

A missing or inaccessible resource returns `404`. An expired conversation or run can return `410`.
Read the affected project resource after an uncertain write.

Project methods accept optional `signal` and `timeoutMs` options as their final argument.
See the [method reference](methods.md) for every argument and return type.
