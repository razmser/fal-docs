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

# realtime

> API reference for @fal-ai/client realtime

## Classes & Interfaces

### RealtimeConnection

```typescript theme={null}
interface RealtimeConnection
```

A connection object that allows you to `send` request payloads to a
realtime endpoint.

> [accordion] Methods
>
> #### send
>
> ```typescript theme={null}
> send(input: Input & Partial<WithRequestId>): void
> ```
>
> | Parameter | Type | Description |
> | :- | :- | :- |
> | `input` | `Input & Partial<WithRequestId>` | - |
>
> #### close
>
> ```typescript theme={null}
> close(): void
> ```

### RealtimeConnectionHandler

```typescript theme={null}
interface RealtimeConnectionHandler
```

Options for connecting to the realtime endpoint.

> [accordion] Properties
>
> | Name | Type | Description |
> | :- | :- | :- |
> | `connectionKey?` | `string` | The connection key. This is used to reuse the same connection across multiple calls to `connect`. This is particularly useful in contexts where the connection is established as part of a component lifecycle (e.g. React) and the component is re-rendered multiple times. |
> | `clientOnly?` | `boolean` | If `true`, the connection will only be established on the client side. This is useful for frameworks that reuse code for both server-side rendering and client-side rendering (e.g. Next.js). This is set to `true` by default when running on React in the server. Otherwise, it is set to `false`. Note that more SSR frameworks might be automatically detected in the future. In the meantime, you can set this to `true` when needed. |
> | `throttleInterval?` | `number` | The throtle duration in milliseconds. This is used to throtle the calls to the `send` function. Realtime apps usually react to user input, which can be very frequent (e.g. fast typing or mouse/drag movements). The default value is `128` milliseconds. |
> | `maxBuffering?` | `number` | Configures the maximum amount of frames to store in memory before starting to drop old ones for in favor of the newer ones. It must be between `1` and `60`. The recommended is `2`. The default is `undefined` so it can be determined by the app (normally is set to the recommended setting). |
> | `path?` | `string` | Optional path to append after the app id. Defaults to `/realtime`. |
> | `encodeMessage?` | `(input: any) => Uint8Array \| string` | Optional encoder for outgoing messages. Defaults to msgpack. Should return either a `Uint8Array` (binary) or string (text frame). |
> | `decodeMessage?` | `(data: any) => Promise<any> \| any` | Optional decoder for incoming messages. Defaults to msgpack with JSON support for string payloads. |
> | `tokenProvider?` | `TokenProvider` | A custom token provider function. When provided, this function will be used to fetch authentication tokens instead of the default internal token fetching mechanism. This is useful when you want to fetch tokens through your own backend proxy. If not provided, the default `getTemporaryAuthToken` will be used. |
> | `tokenExpirationSeconds?` | `number` | The token expiration time in seconds. This is used to determine when to refresh the token. The token will be refreshed at 90% of this value. Only relevant when using a custom `tokenProvider`. If a custom `tokenProvider` is used without specifying this value, automatic token refresh will be disabled. |

> [accordion] Methods
>
> #### onResult
>
> ```typescript theme={null}
> onResult(result: Output & WithRequestId): void
> ```
>
> Callback function that is called when a result is received.
>
> | Parameter | Type | Description |
> | :- | :- | :- |
> | `result` | `Output & WithRequestId` | - The result of the request. |
>
> #### onError
>
> ```typescript theme={null}
> onError(error: ApiError<any>): void
> ```
>
> Callback function that is called when an error occurs.
>
> | Parameter | Type | Description |
> | :- | :- | :- |
> | `error` | `ApiError<any>` | - The error that occurred. |

### RealtimeClient

```typescript theme={null}
interface RealtimeClient
```

> [accordion] Methods
>
> #### connect
>
> ```typescript theme={null}
> connect(app: string, handler: RealtimeConnectionHandler<Output>): RealtimeConnection<Input>
> ```
>
> Connect to the realtime endpoint. The default implementation uses
> WebSockets to connect to fal function endpoints that support WSS.
>
> | Parameter | Type | Description |
> | :- | :- | :- |
> | `app` | `string` | the app alias or identifier. |
> | `handler` | `RealtimeConnectionHandler<Output>` | the connection handler. |
>
> **Returns:** `RealtimeConnection<Input>`

***

## Functions

### createRealtimeClient

```typescript theme={null}
function createRealtimeClient({ config, }: RealtimeClientDependencies): RealtimeClient
```

| Parameter | Type | Description |
| :- | :- | :- |
| `{ config, }` | `RealtimeClientDependencies` | - |

**Returns:** `RealtimeClient`
