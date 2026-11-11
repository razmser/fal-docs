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

# retry

> API reference for @fal-ai/client retry

## Classes & Interfaces

### RetryMetrics

```typescript theme={null}
interface RetryMetrics
```

Retry metrics for tracking retry attempts

> [accordion] Properties
>
> | Name | Type | Description |
> | :- | :- | :- |
> | `totalAttempts` | `number` | - |
> | `totalDelay` | `number` | - |
> | `lastError?` | `any` | - |

***

## Functions

### isRetryableNetworkError

```typescript theme={null}
function isRetryableNetworkError(error: unknown): boolean
```

Returns true for transient transport-level failures (connection resets,
DNS hiccups, socket timeouts, etc.). Mirrors the Python client's behavior
of retrying `httpx.TransportError` and `httpx.TimeoutException`.

| Parameter | Type | Description |
| :- | :- | :- |
| `error` | `unknown` | - |

**Returns:** `boolean`

### isRetryableError

```typescript theme={null}
function isRetryableError(error: any, retryableStatusCodes: number[]): boolean
```

Determines if an error is retryable based on the status code or, for
non-HTTP failures, on the underlying transport error. User-specified
timeouts (504 with X-Fal-Request-Timeout-Type: user) are NOT retryable.

| Parameter | Type | Description |
| :- | :- | :- |
| `error` | `any` | - |
| `retryableStatusCodes` | `number[]` | - |

**Returns:** `boolean`

### calculateBackoffDelay

```typescript theme={null}
function calculateBackoffDelay(attempt: number, baseDelay: number, maxDelay: number, backoffMultiplier: number, enableJitter: boolean): number
```

Calculates the backoff delay for a given attempt using exponential backoff

| Parameter | Type | Description |
| :- | :- | :- |
| `attempt` | `number` | - |
| `baseDelay` | `number` | - |
| `maxDelay` | `number` | - |
| `backoffMultiplier` | `number` | - |
| `enableJitter` | `boolean` | - |

**Returns:** `number`

### executeWithRetry

```typescript theme={null}
async function executeWithRetry(operation: () => Promise<T>, options: RetryOptions, onRetry?: (attempt: number, error: any, delay: number) => void): Promise<{ result: T; metrics: RetryMetrics }>
```

Executes an operation with retry logic and returns both result and metrics

| Parameter | Type | Description |
| :- | :- | :- |
| `operation` | `() => Promise<T>` | - |
| `options` | `RetryOptions` | - |
| `onRetry` | `(attempt: number, error: any, delay: number) => void` | - |

**Returns:** `Promise<{ result: T; metrics: RetryMetrics }>`

***

## Types

### RetryOptions

```typescript theme={null}
type RetryOptions = {
  maxRetries: number;
  baseDelay: number;
  maxDelay: number;
  backoffMultiplier: number;
  retryableStatusCodes: number[];
  enableJitter: boolean;
}
```
