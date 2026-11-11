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

# Media and costs

> Read completed artifacts, reuse media in follow-ups, and select final results.

Use the [quickstart client](quickstart.md#create-a-client). Replace uppercase IDs with your resource IDs.

Pending generation appears as a `fal.operation`.
Completed media appears in `response.artifacts` as a `fal.artifact`.

## Read artifacts

`artifacts.retrieve(id, { revision }?)` returns an `AgentArtifact`. Omit `revision` to read the available version. Media artifacts use revision `1`.
An unavailable revision returns `404`.
Each artifact has an ID, revision, and optional files.
A file includes its role, URL, MIME type, and optional URL expiry.

```ts theme={null}
import { agent } from "./client.ts";

const artifact =
  await agent.artifacts.retrieve("ARTIFACT_ID");
console.log(artifact.files);
```

Artifact reads enforce ownership and retention.
An artifact ID is different from a library asset-record ID.

## Refine an existing result

Reference a completed artifact alongside the new instruction to reuse media without uploading it again.

```ts theme={null}
import { agent } from "./client.ts";

const response = await agent.run({
  conversation: "CONVERSATION_ID",
  input: [
    {
      role: "user",
      content: [
        {
          type: "input_text",
          text: "Make the lighting warmer.",
        },
        {
          type: "fal.input_artifact",
          artifact_id: "ARTIFACT_ID",
        },
      ],
    },
  ],
});

console.log(response.status, response.artifacts);
```

A `fal.input_artifact` part accepts an optional `revision` of `1`. Omit it to use the available artifact.
This creates a new response in the same conversation.
A media refinement can incur new generation charges.

For an external image, use `{ type: "input_image", image_url }`.
For a file, use `{ type: "input_file", file_url, mime_type }`.
The URL must be accessible to the runtime. These content parts reference files and do not upload them.

## Select final results

Completion does not mark every artifact as a final result.
After the response finishes, select final artifacts with its current sequence number.

```ts theme={null}
import { agent } from "./client.ts";

const response =
  await agent.responses.retrieve("RESPONSE_ID");

await agent.responses.selectFinalArtifacts(
  response.id,
  {
    artifact_ids: ["ARTIFACT_ID"],
    expected_sequence_number:
      response.fal.sequence_number,
  },
);
```

The returned `final_artifacts` reflects the selection.
Pass an empty array to clear it. On a `409`, retrieve the current response and apply the selection to that version.

## Inspect generation costs

```ts theme={null}
import { agent } from "./client.ts";

const summary =
  await agent.conversations.generationSummary(
    "CONVERSATION_ID",
  );
console.log(summary);
```

The summary reports generation costs and unpriced requests for that conversation.
`totalCostNanoUsd` and `costsNanoUsd` use billionths of a US dollar. Divide by `1_000_000_000` for USD.
`unpricedRequestCount` counts requests without a price. The reported total excludes those costs and LLM usage.

## Handle partial results

A failed response can retain usable artifacts.
Inspect each operation for generation failures and the response error for execution failure.
Export blocks can contain download links without a corresponding SDK artifact.
Render their `fallback_text` when your application does not understand the block kind.
