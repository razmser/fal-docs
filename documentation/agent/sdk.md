> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# fal Agent SDK for JavaScript and TypeScript

> Install the fal Agent SDK for JavaScript and TypeScript. Generate AI media, stream progress, and handle questions and approvals with code examples.

The fal Agent SDK connects your JavaScript or TypeScript application to [fal Agent](../agent.md).
Describe a task in natural language to generate images, video, audio, or 3D media through the fal model catalog.
The agent chooses models, prepares prompts, and executes the task.

Use the Agent SDK when your application needs the agent to coordinate a task across multiple steps.
It supports conversations, editable plans, user approvals, and generated media.
For a direct call to a specific model, use the [fal model clients](../../api-reference/client-libraries.md) instead.

> [!NOTE]
>
> The Agent SDK is in alpha. These examples use `@fal-ai/client@1.11.0-alpha.4`.
> Media generation can incur charges. See [Access and
> pricing](access-and-pricing.md).

## Install the SDK

The Agent SDK is part of the [`@fal-ai/client` npm package](https://www.npmjs.com/package/@fal-ai/client).
It supports JavaScript and includes TypeScript types for requests, responses, and resources.
Use Node.js 22.22 or later for these examples.
See [client configuration](sdk/configuration.md#runtime-requirements) for runtime requirements.

```bash npm theme={null}
npm install @fal-ai/client@1.11.0-alpha.4
```

```bash yarn theme={null}
yarn add @fal-ai/client@1.11.0-alpha.4
```

```bash pnpm theme={null}
pnpm add @fal-ai/client@1.11.0-alpha.4
```

### Python support

The Agent SDK currently supports JavaScript and TypeScript.
The [fal Python client](../../api-reference/client-libraries/python.md) supports direct model calls.
It does not provide the Agent SDK methods for conversations, plans, or approvals.

### Authentication

Create a [fal API key](../setting-up/authentication.md) with the **AGENT** permission preset.
Set the key in your environment:

```bash theme={null}
export FAL_KEY="YOUR_API_KEY"
```

Replace `YOUR_API_KEY` with your key.
The SDK reads `FAL_KEY` when you omit `credentials`.
Keep the API key on your server.
For a browser application, use a server endpoint that enforces your users' permissions.
See [browser applications](sdk/configuration.md#browser-applications).

## Generate media

Use `agent.run()` to submit a task and wait for its result.
The agent creates a response that contains the task status, assistant text, generated media, and any questions or approvals.
An artifact identifies generated media and can contain files with download URLs.

Create `agent.mts`:

```ts theme={null}
import { createFalClient } from "@fal-ai/client";

const agent = createFalClient().agent;

const response = await agent.run(
  { input: "Generate a product photo of a blue ceramic mug." },
  { timeoutMs: 5 * 60 * 1000 },
);

console.log(response.id, response.status, response.output_text);
console.log(response.pending_inputs, response.error);

for (const artifact of response.artifacts) {
  for (const file of artifact.files ?? []) {
    console.log(file.url);
  }
}
```

Install `tsx` to execute the TypeScript example:

```bash theme={null}
npm install --save-dev tsx
npx tsx agent.mts
```

`run()` returns when execution stops or needs user input.
Check the response before you use its media:

| Field | Purpose |
| - | - |
| `status` | Reports the task's execution state. |
| `output_text` | Contains the assistant's text. |
| `artifacts` | Contains generated media and optional files with URLs. |
| `pending_inputs` | Contains questions and approvals that need the user's answer. |

Execution failures appear in `response.status` and `response.error`.
Request failures throw `AgentRequestError`.
See [errors and troubleshooting](sdk/errors.md) for failure and recovery details.
See [media and costs](sdk/media.md) to reuse media or select final results.

### SDK methods

Use these methods to submit tasks and control their execution:

| SDK method | Purpose |
| - | - |
| `agent.run()` | Submit a new task and poll until execution stops. |
| `agent.stream()` | Submit a new task and stream progress. |
| `agent.responses.create()` | Submit a new task. Return its initial response. |
| `agent.responses.retrieve()` | Read the current state of an existing task. |
| `agent.responses.wait()` | Poll an existing task until execution stops. |
| `agent.responses.stream()` | Stream an existing task. |
| `agent.responses.answer()` | Answer a question or approval for an existing task. |
| `agent.responses.cancel()` | Request cancellation of an existing task. |

See the [method reference](sdk/methods.md) for all methods and the [type reference](sdk/types.md) for their types.

## Progress and user input

### Stream progress

Use `agent.stream()` to start a task with progress updates:

```ts theme={null}
import { createFalClient } from "@fal-ai/client";

const agent = createFalClient().agent;

for await (const response of agent.stream(
  { input: "Generate a product photo of a blue ceramic mug." },
  { timeoutMs: 5 * 60 * 1000 },
)) {
  console.log(response.status, response.output_text);
  console.log(response.artifacts, response.pending_inputs);
}
```

This example creates a new task.
Use `responses.stream(responseId)` to observe a task that already exists.

Each update contains the complete response state, called a snapshot.
Replace the displayed state on each update.
Do not append each snapshot's `output_text` to the previous text.

### Questions and approvals

The agent can pause to ask a question or request approval.
When `pending_inputs` contains a request, show it to the user.
Collect the user's answer.
Submit the answer with `responses.answer()` and the same response ID.
Then resume observation with `responses.wait()` or `responses.stream()`.

See [questions and approvals](sdk/inputs.md) for answer formats and examples.

### Recovery and cancellation

Save the response ID to reconnect after a process restart, timeout, or lost connection.
Use `responses.wait()` or `responses.stream()` with that ID to observe the existing task.
For an application that stores task state, submit the task with `responses.create()`.
Save the response ID.
Then call `responses.wait()` or `responses.stream()`.
See [responses and recovery](sdk/responses.md) for submission retries and reconnection options.

> [!WARNING]
>
> A timeout, an aborted signal, or a closed stream does not cancel server
> execution. Use `responses.cancel()` to request cancellation of the task.

## Next steps

* Use the [quickstart](sdk/quickstart.md) to run your first task.
* Configure credentials, transport, and retries with [client configuration](sdk/configuration.md).
* Control a task's steps with [plans](sdk/plans.md).
* Organize tasks with [conversations](sdk/resources.md) and [projects](sdk/projects.md).
* Add reusable instructions with [skills](sdk/skills.md).
* Display execution progress with [tool activity](sdk/activity.md).
* Find integration guides in [guidance for coding agents](sdk/for-agents.md).
