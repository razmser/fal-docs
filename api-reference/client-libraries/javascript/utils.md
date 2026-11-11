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

# utils

> API reference for @fal-ai/client utils

***

## Functions

### ensureEndpointIdFormat

```typescript theme={null}
function ensureEndpointIdFormat(id: string): string
```

| Parameter | Type | Description |
| :- | :- | :- |
| `id` | `string` | - |

**Returns:** `string`

### parseEndpointId

```typescript theme={null}
function parseEndpointId(id: string): EndpointId
```

| Parameter | Type | Description |
| :- | :- | :- |
| `id` | `string` | - |

**Returns:** `EndpointId`

### resolveEndpointPath

```typescript theme={null}
function resolveEndpointPath(app: string, path: string | undefined, defaultPath: string): string | undefined
```

Resolves the endpoint path, normalizing it and applying a default.
If no explicit path is provided and the app already ends with the
default path, returns undefined to avoid duplication.

| Parameter | Type | Description |
| :- | :- | :- |
| `app` | `string` | - The app/endpoint identifier |
| `path` | `string \| undefined` | - An explicitly provided path (always used if present) |
| `defaultPath` | `string` | - The default path to use (e.g. "/realtime") |

**Returns:** `string | undefined`

### isValidUrl

```typescript theme={null}
function isValidUrl(url: string): any
```

| Parameter | Type | Description |
| :- | :- | :- |
| `url` | `string` | - |

**Returns:** `any`

### throttle

```typescript theme={null}
function throttle(func: T, limit: number, leading?: any): (...funcArgs: Parameters<T>) => ReturnType<T> | void
```

| Parameter | Type | Description |
| :- | :- | :- |
| `func` | `T` | - |
| `limit` | `number` | - |
| `leading` | `any` | - |

**Returns:** `(...funcArgs: Parameters<T>) => ReturnType<T> | void`

### isReact

```typescript theme={null}
function isReact(): any
```

Not really the most optimal way to detect if we're running in React,
but the idea here is that we can support multiple rendering engines
(starting with React), with all their peculiarities, without having
to add a dependency or creating custom integrations (e.g. custom hooks).

Yes, a bit of magic to make things works out-of-the-box.

**Returns:** `any`

### isPlainObject

```typescript theme={null}
function isPlainObject(value: any): boolean
```

Check if a value is a plain object.

| Parameter | Type | Description |
| :- | :- | :- |
| `value` | `any` | - The value to check. |

**Returns:** `boolean`

### sleep

```typescript theme={null}
async function sleep(ms: number): Promise<void>
```

Utility function to sleep for a given number of milliseconds

| Parameter | Type | Description |
| :- | :- | :- |
| `ms` | `number` | - |

**Returns:** `Promise<void>`

***

## Types

### EndpointId

```typescript theme={null}
type EndpointId = {
  readonly owner: string;
  readonly alias: string;
  readonly path?: string;
  readonly namespace?: EndpointNamespace;
}
```
