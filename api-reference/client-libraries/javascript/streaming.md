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

# streaming

> API reference for @fal-ai/client streaming

## Classes & Interfaces

### FalStream

```typescript theme={null}
interface FalStream
```

The class representing a streaming response. With t

> [accordion] Properties
>
> | Name | Type | Description |
> | :- | :- | :- |
> | `config` | `RequiredConfig` | - |
> | `endpointId` | `string` | - |
> | `url` | `string` | - |
> | `options` | `StreamOptions<Input>` | - |
> | `listeners` | `Map<FalStreamEventType, EventHandler[]>` | - |
> | `buffer` | `Output[]` | - |
> | `currentData` | `Output \| undefined` | - |
> | `lastEventTimestamp` | `any` | - |
> | `streamClosed` | `any` | - |
> | `_requestId` | `string \| null` | - |
> | `donePromise` | `Promise<Output>` | - |
> | `abortController` | `any` | - |
> | `start` | `any` | - |
> | `handleResponse` | `any` | - |
> | `handleError` | `any` | - |
> | `on` | `any` | - |
> | `emit` | `any` | - |
> | `done` | `any` | Gets a reference to the `Promise` that indicates whether the streaming is done or not. Developers should always call this in their apps to ensure the request is over. An alternative to this, is to use `on('done')` in case your application architecture works best with event listeners. |
> | `abort` | `any` | Aborts the streaming request. **Note:** This method is noop in case the request is already done. |

> [accordion] Methods
>
> #### `[Symbol.asyncIterator]`
>
> ```typescript theme={null}
> async [Symbol.asyncIterator](): any
> ```
>
> **Returns:** `any`

### StreamingClient

```typescript theme={null}
interface StreamingClient
```

The streaming client interface.

> [accordion] Methods
>
> #### stream
>
> ```typescript theme={null}
> stream(endpointId: Id, options: StreamOptions<InputType<Id>>): Promise<FalStream<InputType<Id>, OutputType<Id>>>
> ```
>
> Calls a fal app that supports streaming and provides a streaming-capable
> object as a result, that can be used to get partial results through either
> `AsyncIterator` or through an event listener.
>
> | Parameter | Type | Description |
> | :- | :- | :- |
> | `endpointId` | `Id` | the endpoint id, e.g. `fal-ai/llavav15-13b`. |
> | `options` | `StreamOptions<InputType<Id>>` | the request options, including the input payload. |
>
> **Returns:** `Promise<FalStream<InputType<Id>, OutputType<Id>>>`

***

## Functions

### createStreamingClient

```typescript theme={null}
function createStreamingClient({ config, storage, }: StreamingClientDependencies): StreamingClient
```

| Parameter | Type | Description |
| :- | :- | :- |
| `{ config, storage, }` | `StreamingClientDependencies` | - |

**Returns:** `StreamingClient`

***

## Types

### StreamingConnectionMode

```typescript theme={null}
type StreamingConnectionMode = "client" | "server"
```

### StreamOptions

```typescript theme={null}
type StreamOptions = {
  /**
   * The endpoint URL. If not provided, it will be generated from the
   * `endpointId` and the `queryParams`.
   */
  readonly url?: string;

  /**
   * The API input payload.
   */
  readonly input?: Input;

  /**
   * The query parameters to be sent with the request.
   */
  readonly queryParams?: Record<string, string>;

  /**
   * The maximum time interval in milliseconds between stream chunks. Defaults to 15s.
   */
  readonly timeout?: number;

  /**
   * Whether it should auto-upload File-like types to fal's storage
   * or not.
   */
  readonly autoUpload?: boolean;

  /**
   * The HTTP method, defaults to `post`;
   */
  readonly method?: "get" | "post" | "put" | "delete" | string;

  /**
   * The content type the client accepts as response.
   * By default this is set to `text/event-stream`.
   */
  readonly accept?: string;

  /**
   * The streaming connection mode. This is used to determine
   * whether the streaming will be done from the browser itself (client)
   * or through your own server, either when running on NodeJS or when
   * using a proxy that supports streaming.
   *
   * It defaults to `server`. Set to `client` if your server proxy doesn't
   * support streaming.
   */
  readonly connectionMode?: StreamingConnectionMode;

  /**
   * The signal to abort the request.
   */
  readonly signal?: AbortSignal;

  /**
   * A custom token provider function. Only used when `connectionMode` is `"client"`.
   * When provided, this function will be used to fetch authentication tokens
   * instead of the default internal token fetching mechanism.
   */
  readonly tokenProvider?: TokenProvider;
}
```

The stream API options. It requires the API input and also
offers configuration options.
