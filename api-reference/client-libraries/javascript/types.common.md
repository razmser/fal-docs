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

# types.common

> API reference for @fal-ai/client types.common

## Classes & Interfaces

### InQueueQueueStatus

```typescript theme={null}
interface InQueueQueueStatus
```

> [accordion] Properties
>
> | Name | Type | Description |
> | :- | :- | :- |
> | `status` | `"IN_QUEUE"` | - |
> | `queue_position` | `number` | - |

### InProgressQueueStatus

```typescript theme={null}
interface InProgressQueueStatus
```

> [accordion] Properties
>
> | Name | Type | Description |
> | :- | :- | :- |
> | `status` | `"IN_PROGRESS"` | - |
> | `logs` | `RequestLog[]` | - |

### CompletedQueueStatus

```typescript theme={null}
interface CompletedQueueStatus
```

> [accordion] Properties
>
> | Name | Type | Description |
> | :- | :- | :- |
> | `status` | `"COMPLETED"` | - |
> | `logs` | `RequestLog[]` | - |
> | `metrics?` | `Metrics` | - |

***

## Functions

### isQueueStatus

```typescript theme={null}
function isQueueStatus(obj: any): obj is QueueStatus
```

| Parameter | Type | Description |
| :- | :- | :- |
| `obj` | `any` | - |

**Returns:** `obj is QueueStatus`

### isCompletedQueueStatus

```typescript theme={null}
function isCompletedQueueStatus(obj: any): obj is CompletedQueueStatus
```

| Parameter | Type | Description |
| :- | :- | :- |
| `obj` | `any` | - |

**Returns:** `obj is CompletedQueueStatus`

***

## Types

### Result

```typescript theme={null}
type Result = {
  data: T;
  requestId: string;
}
```

Represents an API result, containing the data,
the request ID and any other relevant information.

### RunOptions

```typescript theme={null}
type RunOptions = {
  /**
   * The function input. It will be submitted either as query params
   * or the body payload, depending on the `method`.
   */
  readonly input?: Input;

  /**
   * The HTTP method, defaults to `post`;
   */
  readonly method?: "get" | "post" | "put" | "delete" | string;

  /**
   * The abort signal to cancel the request.
   */
  readonly abortSignal?: AbortSignal;

  /**
   * Object lifecycle configuration for controlling how long generated objects
   * (images, files, etc.) remain available before expiring.
   *
   * @see StorageSettings
   * @see https://docs.fal.ai/model-apis/model-endpoints/queue#object-lifecycle
   */
  readonly storageSettings?: StorageSettings;

  /**
   * Server-side request timeout in seconds. Limits total time spent waiting
   * before processing starts (includes queue wait, retries, and routing).
   * Does not apply once the application begins processing.
   *
   * This will be sent as the `x-fal-request-start-timeout` header.
   */
  readonly startTimeout?: number;
}
```

The function input and other configuration when running
the function, such as the HTTP method to use.

### UrlOptions

```typescript theme={null}
type UrlOptions = {
  /**
   * If `true`, the function will use the queue to run the function
   * asynchronously and return the result in a separate call. This
   * influences how the URL is built.
   */
  readonly subdomain?: string;

  /**
   * The query parameters to include in the URL.
   */
  readonly query?: Record<string, string>;

  /**
   * The path to append to the function URL.
   */
  path?: string;
}
```

### RequestLog

```typescript theme={null}
type RequestLog = {
  message: string;
  level: "STDERR" | "STDOUT" | "ERROR" | "INFO" | "WARN" | "DEBUG";
  source: "USER";
  timestamp: string; // Using string to represent date-time format, but you could also use 'Date' type if you're going to construct Date objects.
}
```

### Metrics

```typescript theme={null}
type Metrics = {
  inference_time: number | null;
}
```

### QueueStatus

```typescript theme={null}
type QueueStatus = | InProgressQueueStatus
  | CompletedQueueStatus
  | InQueueQueueStatus
```

### ValidationErrorInfo

```typescript theme={null}
type ValidationErrorInfo = {
  msg: string;
  loc: Array<string | number>;
  type: string;
}
```

### WebHookResponse

```typescript theme={null}
type WebHookResponse = | {
      /** Indicates a successful response. */
      status: "OK";
      /** The payload of the response, structure determined by the Payload type. */
      payload: Payload;
      /** Error is never present in a successful response. */
      error: never;
      /** The unique identifier for the request. */
      request_id: string;
    }
  | {
      /** Indicates an unsuccessful response. */
      status: "ERROR";
      /** The payload of the response, structure determined by the Payload type. */
      payload: Payload;
      /** Description of the error that occurred. */
      error: string;
      /** The unique identifier for the request. */
      request_id: string;
    }
```

Represents the response from a WebHook request.
This is a union type that varies based on the `status` property.
