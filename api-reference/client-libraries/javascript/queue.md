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

# queue

> API reference for @fal-ai/client queue

## Classes & Interfaces

### QueueClient

```typescript theme={null}
interface QueueClient
```

Represents a request queue with methods for submitting requests,
checking their status, retrieving results, and subscribing to updates.

> [accordion] Methods
>
> #### submit
>
> ```typescript theme={null}
> submit(endpointId: Id, options: SubmitOptions<InputType<Id>>): Promise<InQueueQueueStatus>
> ```
>
> Submits a request to the queue.
>
> | Parameter | Type | Description |
> | :- | :- | :- |
> | `endpointId` | `Id` | - The ID of the function web endpoint. |
> | `options` | `SubmitOptions<InputType<Id>>` | - Options to configure how the request is run. |
>
> **Returns:** `Promise<InQueueQueueStatus>`
>
> #### status
>
> ```typescript theme={null}
> status(endpointId: string, options: QueueStatusOptions): Promise<QueueStatus>
> ```
>
> Retrieves the status of a specific request in the queue.
>
> | Parameter | Type | Description |
> | :- | :- | :- |
> | `endpointId` | `string` | - The ID of the function web endpoint. |
> | `options` | `QueueStatusOptions` | - Options to configure how the request is run. |
>
> **Returns:** `Promise<QueueStatus>`
>
> #### streamStatus
>
> ```typescript theme={null}
> streamStatus(endpointId: string, options: QueueStatusStreamOptions): Promise<FalStream<unknown, QueueStatus>>
> ```
>
> Subscribes to updates for a specific request in the queue using HTTP streaming events.
>
> | Parameter | Type | Description |
> | :- | :- | :- |
> | `endpointId` | `string` | - The ID of the function web endpoint. |
> | `options` | `QueueStatusStreamOptions` | - Options to configure how the request is run and how updates are received. |
>
> **Returns:** `Promise<FalStream<unknown, QueueStatus>>`
>
> #### subscribeToStatus
>
> ```typescript theme={null}
> subscribeToStatus(endpointId: string, options: QueueStatusSubscriptionOptions): Promise<CompletedQueueStatus>
> ```
>
> Subscribes to updates for a specific request in the queue using polling or streaming.
> See `options.mode` for more details.
>
> | Parameter | Type | Description |
> | :- | :- | :- |
> | `endpointId` | `string` | - The ID of the function web endpoint. |
> | `options` | `QueueStatusSubscriptionOptions` | - Options to configure how the request is run and how updates are received. |
>
> **Returns:** `Promise<CompletedQueueStatus>`
>
> #### result
>
> ```typescript theme={null}
> result(endpointId: Id, options: BaseQueueOptions): Promise<Result<OutputType<Id>>>
> ```
>
> Retrieves the result of a specific request from the queue.
>
> | Parameter | Type | Description |
> | :- | :- | :- |
> | `endpointId` | `Id` | - The ID of the function web endpoint. |
> | `options` | `BaseQueueOptions` | - Options to configure how the request is run. |
>
> **Returns:** `Promise<Result<OutputType<Id>>>`
>
> #### cancel
>
> ```typescript theme={null}
> cancel(endpointId: string, options: BaseQueueOptions): Promise<void>
> ```
>
> Cancels a request in the queue.
>
> | Parameter | Type | Description |
> | :- | :- | :- |
> | `endpointId` | `string` | - The ID of the function web endpoint. |
> | `options` | `BaseQueueOptions` | - Options to configure how the request is run and how updates are received. |
>
> **Returns:** `Promise<void>`

***

## Types

### QueuePriority

```typescript theme={null}
type QueuePriority = "low" | "normal"
```

### QueueStatusSubscriptionOptions

```typescript theme={null}
type QueueStatusSubscriptionOptions = QueueStatusOptions &
  QueueModeOptions &
  Omit<QueueCommonSubscribeOptions, "onEnqueue" | "webhookUrl">
```

### QueueSubscribeOptions

```typescript theme={null}
type QueueSubscribeOptions = QueueCommonSubscribeOptions &
  QueueModeOptions
```

Options for subscribing to the request queue.

### SubmitOptions

```typescript theme={null}
type SubmitOptions = RunOptions<Input> & {
  /**
   * The URL to send a webhook notification to when the request is completed.
   * @see WebHookResponse
   */
  webhookUrl?: string;

  /**
   * The priority of the request. It defaults to `normal`.
   * This will be sent as the `x-fal-queue-priority` header.
   *
   * @see QueuePriority
   */
  priority?: QueuePriority;

  /**
   * A hint for the runner to use when processing the request.
   * This will be sent as the `x-fal-runner-hint` header.
   */
  hint?: string;

  /**
   * Server-side request timeout in seconds. Limits total time spent waiting
   * before processing starts (includes queue wait, retries, and routing).
   * Does not apply once the application begins processing.
   *
   * This will be sent as the `x-fal-request-start-timeout` header.
   */
  startTimeout?: number;

  /**
   * Additional HTTP headers to include in the submit request.
   *
   * Note: `priority`, `hint`, `startTimeout`, and `objectLifecycle` will override the following headers:
   *  - `x-fal-queue-priority`
   *  - `x-fal-runner-hint`
   *  - `x-fal-request-start-timeout`
   *  - `x-fal-object-lifecycle-preference`
   */
  headers?: Record<string, string>;
}
```

Options for submitting a request to the queue.

### QueueStatusOptions

```typescript theme={null}
type QueueStatusOptions = BaseQueueOptions & {
  /**
   * If `true`, the response will include the logs for the request.
   * Defaults to `false`.
   */
  logs?: boolean;
}
```

### QueueStatusStreamOptions

```typescript theme={null}
type QueueStatusStreamOptions = QueueStatusOptions & {
  /**
   * The connection mode to use for streaming updates. It defaults to `server`.
   * Set to `client` if your server proxy doesn't support streaming.
   */
  connectionMode?: StreamingConnectionMode;
}
```
