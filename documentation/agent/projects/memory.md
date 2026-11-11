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

# Memory and documents

> How project memory captures decisions and preferences, and how uploaded documents become project context.

Project memory lets the agent recall what happened in earlier chats. It has two parts: notes distilled from the chats, and documents you upload.

## Memory notes

After each turn in a project chat, fal Agent ingests the turn into the project's memory. A background pass distills notes and a short primer that summarizes the project. Notes have a kind:

| Kind | Example |
| :- | :- |
| **Decision** | "Final palette is teal and rust." |
| **Preference** | "Prefers 4:5 for all product shots." |
| **Style** | "35mm, shallow depth of field, warm grade." |
| **Entity** | "Mara is the lead character. Red coat, silver hair." |
| **Fact** | "The launch date is March 12." |

Notes are short, standalone statements. A new note that repeats an old one replaces it.

### Manage notes

Open the rail's **Context** tab to see the notes. You can pin a note so it is always in context, edit its text, change its kind, or delete it. You can also ask the agent to remember something directly:

```text theme={null}
Remember: every video ends on the logo card, 1.5 seconds, no audio.
```

### How the agent uses memory

At the start of a turn the agent receives the primer and the pinned notes. It can search the rest of the memory, including verbatim history from earlier chats. It can also search the project's media by text or by image similarity. It saves a new note when a decision is made.

## Documents

Upload PDF, DOCX, TXT, Markdown, CSV, TSV, or JSON files with **Add resource → Upload documents**. You can also drop them on the project page. Documents become project context.

* The text is split into chunks and indexed for search. A document is indexed up to about 250,000 characters.
* A one-line summary of each document appears in the document roster. The summary says what the document contains and what it is useful for.
* The agent reads a document in pages when it needs the full text.

Documents uploaded to a project are indexed far more deeply than a document attached to one message, which is read only in part. Put briefs, scripts, and brand guides in the project.

A document's status shows **Ready**, **No text** when nothing could be extracted, or **Failed**. Failed documents can be retried.

## Memory and retention

Derived memory and indexed documents have no expiry. Both are turned off when a finite [retention policy](../retention.md) applies to the workspace.
