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

# Agent SDK quickstart

> Install the TypeScript SDK, run your first task, and read the result.

Generate an image from a Node.js application. Use Node.js 22.22 or later for these examples.

## Install

The Agent SDK is in alpha. Install the version used in this guide:

```bash theme={null}
npm install @fal-ai/client@1.11.0-alpha.4
npm install --save-dev tsx
```

## Configure your credentials

Create a fal API key with the **AGENT** permission preset.
Set your API key in the environment:

```bash theme={null}
export FAL_KEY="YOUR_API_KEY"
```

Replace `YOUR_API_KEY` with your key.
The SDK sends Agent requests to `https://fal.ai/api/agent-v2/sdk`.
Keep the API key on your server. Do not include it in browser code.

## Create a client

Create `client.ts`. The other examples in this guide import this client.

```ts theme={null}
import { createFalClient } from "@fal-ai/client";

export const agent = createFalClient().agent;
```

## Run your first task

Create `agent.mts` next to `client.ts`:

```ts theme={null}
import { agent } from "./client.ts";

const response = await agent.run({
  input:
    "Generate a product photo of a blue ceramic mug.",
});

console.log(response.status, response.output_text);
console.log(
  response.artifacts,
  response.pending_inputs,
  response.error,
);
```

Run the file:

```bash theme={null}
npx tsx agent.mts
```

`run()` returns when execution stops or needs user input. Check `status` and `fal.phase` before using the result.
Media generation can incur charges.

## Stream progress

Use `stream()` to observe updates as the task runs:

```ts theme={null}
import { agent } from "./client.ts";

for await (const response of agent.stream({
  input: "Photograph a blue mug.",
})) {
  console.log(response.output_text);
}
```

Each update contains the complete response snapshot.
Replace the displayed text on each update instead of appending it.
This example starts a new task. Use `responses.stream(responseId)` to observe an existing task.

## Continue the conversation

Replace `CONVERSATION_ID` with `response.fal.conversation_id` from the earlier response:

```ts theme={null}
import { agent } from "./client.ts";

const response = await agent.run({
  conversation: "CONVERSATION_ID",
  input:
    "Make another version with warmer lighting.",
});

console.log(response.output_text);
```

A follow-up creates a new response in the same conversation.
To answer a pending question, use `responses.answer()` with the existing response ID.

## Next steps

* [Save response IDs and recover interrupted requests](responses.md).
* [Answer questions and approvals](inputs.md).
* [Edit and run a plan](plans.md).
* [Reuse generated media](media.md).
