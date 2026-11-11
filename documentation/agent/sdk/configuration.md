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

# Client configuration

> Configure authentication, transport, retries, and the public Agent exports.

Create one client for the credentials your application uses.
Agent requests use `https://fal.ai/api/agent-v2/sdk`.

```ts theme={null}
import { createFalClient } from "@fal-ai/client";

const agent = createFalClient({
  credentials: process.env.FAL_KEY,
  retry: { maxRetries: 3 },
}).agent;
```

See the [quickstart](quickstart.md) for environment setup and a runnable task.

## Configuration fields

| Field | Behavior |
| - | - |
| `credentials` | API key string or synchronous function that returns a string or `undefined`. |
| `fetch` | Custom fetch implementation. Defaults to the runtime's global `fetch`. |
| `retry` | Partial retry configuration. Omitted fields use the defaults below. |
| `requestMiddleware` | Function that receives request configuration and returns a promise with the updated configuration. |
| `proxyUrl` | Shared fal client proxy configuration. The proxy must support the Agent paths and streaming. |
| `suppressLocalCredentialsWarning` | Suppress the browser credential warning. This does not protect an exposed key. |
| `responseHandler` | Shared fal client configuration. Agent JSON calls use the SDK's JSON response handler. |

Use a key with the **AGENT** permission preset. The SDK authenticates requests with `Authorization: Key <key_id>:<key_secret>`.
Browser sessions do not authenticate SDK requests.
The key acts on its account's resources. It does not inherit a signed-in browser user's account selection or administrator role.

When `credentials` is omitted, the shared client reads `FAL_KEY` from the environment.
It also supports the `FAL_KEY_ID` and `FAL_KEY_SECRET` pair.

## Browser applications

Keep the API key on your server. Use a server endpoint to call the Agent SDK for browser clients.
The server must enforce your application's user permissions before forwarding requests.
Your proxy must forward Agent requests and preserve streaming responses.

`proxyUrl` accepts a URL string or `{ url, when }`.
The `when` value is `"browser"`, `"always"`, or a function that receives `{ isBrowser }` and returns a boolean.
A URL string uses the `"browser"` behavior.

`requestMiddleware` receives `{ url, method, headers? }` and returns a promise containing the same shape.
Headers map names to strings or string arrays.
Preserve the required authorization, content type, and idempotency headers when changing a request.

## Request retries

| Retry field | Default | Meaning |
| - | - | - |
| `maxRetries` | `3` | Additional attempts after a retryable failure. |
| `baseDelay` | `1000` | Initial retry delay in milliseconds. |
| `maxDelay` | `30000` | Maximum base delay in milliseconds. |
| `backoffMultiplier` | `2` | Delay multiplier between attempts. |
| `retryableStatusCodes` | `[429, 502, 503, 504]` | HTTP failures eligible for retry. |
| `enableJitter` | `true` | Add variation to retry delays. |

Reads and response commands use this retry policy for eligible HTTP and transport failures.
Resource writes for projects, settings, preferences, skills, library, queues, runs, and final selections are sent once.
Idle conversation creation is also sent once.
Conversation rename and deletion can use automatic retries. Read the conversation after a network error before repeating either operation.

A retry within one command preserves its idempotency key.
To recover after a process restart, persist the request and key before submission.
See [response recovery](responses.md#recover-an-uncertain-submission).

`timeoutMs` covers local request attempts and waits. An abort or timeout stops retries.
Neither cancels server execution.
Streaming also has `maxReconnects` and `reconnectDelayMs`. See [response options](responses.md#options).

## Public exports

| Export | Use |
| - | - |
| `createFalClient(config).agent` | Create the configured Agent client. |
| `AgentClient` | Type for the full Agent client. See the [method reference](methods.md). |
| `AgentResponsesClient` | Type for `agent.responses`. |
| `isAgentTerminal(response)` | True for `completed`, `incomplete`, `failed`, or `cancelled`. |
| `isAgentStopped(response)` | True for terminal responses, `waiting_for_input`, or a present `fal.pending_submission`. |
| `AgentRequestError` | Request failure with optional response and retry context. |
| `AgentProtocolError` | Invalid response or stream protocol. |
| `Agent*` data types | Complete definitions in the [type reference](types.md). |

```ts theme={null}
import { agent } from "./client.ts";
import {
  isAgentStopped,
  isAgentTerminal,
} from "@fal-ai/client";

const response =
  await agent.responses.retrieve("RESPONSE_ID");
console.log(
  isAgentStopped(response),
  isAgentTerminal(response),
);
```

`createAgentClient(config)` accepts a resolved `RequiredConfig`.
For standard setup, use `createFalClient()` to supply transport and retry defaults.

## Runtime requirements

The client uses `fetch`, `AbortController`, `URL`, and asynchronous iterators.
These guides use Node.js 22.22 or later, including its global `File` constructor for document uploads.

Use `agent.projects.documents.upload` to upload and import a project document.
For other uploads, use the shared fal storage client and pass the resulting URL to an Agent request or resource method.
