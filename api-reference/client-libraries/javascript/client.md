> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../../documentation/quickstart.md) and the [Model APIs overview](../../../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# client

> API reference for @fal-ai/client client

## Classes & Interfaces

### FalClient

```typescript theme={null}
interface FalClient
```

The main client type, it provides access to simple API model usage,
as well as access to the `queue` and `storage` APIs.

> [accordion] Properties
>
> | Name | Type | Description |
> | :- | :- | :- |
> | `queue` | `QueueClient` | The queue client to interact with the queue API. |
> | `realtime` | `RealtimeClient` | The realtime client to interact with the realtime API and receive updates in real-time. |
> | `storage` | `StorageClient` | The storage client to interact with the storage API. |
> | `streaming` | `StreamingClient` | The streaming client to interact with the streaming API. |
> | `stream` | `StreamingClient["stream"]` | Calls a fal app that supports streaming and provides a streaming-capable object as a result, that can be used to get partial results through either `AsyncIterator` or through an event listener. |

> [accordion] Methods
>
> #### run
>
> ```typescript theme={null}
> run(endpointId: Id, options: RunOptions<InputType<Id>>): Promise<Result<OutputType<Id>>>
> ```
>
> Runs a fal endpoint identified by its `endpointId`.
>
> | Parameter | Type | Description |
> | :- | :- | :- |
> | `endpointId` | `Id` | The endpoint id, e.g. `fal-ai/fast-sdxl`. |
> | `options` | `RunOptions<InputType<Id>>` | The request options, including the input payload. |
>
> **Returns:** `Promise<Result<OutputType<Id>>>`
>
> #### subscribe
>
> ```typescript theme={null}
> subscribe(endpointId: Id, options: RunOptions<InputType<Id>> & QueueSubscribeOptions): Promise<Result<OutputType<Id>>>
> ```
>
> Subscribes to updates for a specific request in the queue.
>
> | Parameter | Type | Description |
> | :- | :- | :- |
> | `endpointId` | `Id` | - The ID of the API endpoint. |
> | `options` | `RunOptions<InputType<Id>> & QueueSubscribeOptions` | - Options to configure how the request is run and how updates are received. |
>
> **Returns:** `Promise<Result<OutputType<Id>>>`

***

## Functions

### createFalClient

```typescript theme={null}
function createFalClient(userConfig?: Config): FalClient
```

Creates a new reference of the `FalClient`.

| Parameter | Type | Description |
| :- | :- | :- |
| `userConfig` | `Config` | Optional configuration to override the default settings. |

**Returns:** `FalClient`
