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

# storage

> API reference for @fal-ai/client storage

## Classes & Interfaces

### StorageACLRule

```typescript theme={null}
interface StorageACLRule
```

> [accordion] Properties
>
> | Name | Type | Description |
> | :- | :- | :- |
> | `user` | `string` | - |
> | `decision` | `StorageACLDecision` | - |

### StorageACL

```typescript theme={null}
interface StorageACL
```

> [accordion] Properties
>
> | Name | Type | Description |
> | :- | :- | :- |
> | `default?` | `StorageACLDecision` | - |
> | `rules?` | `StorageACLRule[]` | - |

### StorageSettings

```typescript theme={null}
interface StorageSettings
```

Configuration for object lifecycle and storage behavior.

> [accordion] Properties
>
> | Name | Type | Description |
> | :- | :- | :- |
> | `expiresIn?` | `ObjectExpiration` | The expiration time for the stored files (images, videos, etc.). You can specify one of the enumerated values or a number of seconds. |
> | `initialAcl?` | `StorageACL` | Optional ACL configuration applied to the uploaded object. |

### StorageClient

```typescript theme={null}
interface StorageClient
```

File support for the client. This interface establishes the contract for
uploading files to the server and transforming the input to replace file
objects with URLs.

> [accordion] Properties
>
> | Name | Type | Description |
> | :- | :- | :- |
> | `upload` | `(file: Blob, options?: UploadOptions) => Promise<string>` | Upload a file to the server. Returns the URL of the uploaded file. |
> | `transformInput` | `(input: Record<string, any>) => Promise<Record<string, any>>` | Transform the input to replace file objects with URLs. This is used to transform the input before sending it to the server and ensures that the server receives URLs instead of file objects. |

***

## Functions

### getExpirationDurationSeconds

```typescript theme={null}
function getExpirationDurationSeconds(lifecycle: StorageSettings): number | undefined
```

Converts an `StorageSettings` to the expiration duration in seconds.

| Parameter | Type | Description |
| :- | :- | :- |
| `lifecycle` | `StorageSettings` | the lifecycle preference |

**Returns:** `number | undefined`

### buildObjectLifecycleHeaders

```typescript theme={null}
function buildObjectLifecycleHeaders(lifecycle: StorageSettings | undefined): Record<string, string>
```

Builds the headers for the Object Lifecycle preference to be used in API requests.
This is used by the queue and run APIs to control the lifecycle of generated objects.

| Parameter | Type | Description |
| :- | :- | :- |
| `lifecycle` | `StorageSettings \| undefined` | the lifecycle preference |

**Returns:** `Record<string, string>`

### createStorageClient

```typescript theme={null}
function createStorageClient({ config, }: StorageClientDependencies): StorageClient
```

| Parameter | Type | Description |
| :- | :- | :- |
| `{ config, }` | `StorageClientDependencies` | - |

**Returns:** `StorageClient`

***

## Types

### UploadOptions

```typescript theme={null}
type UploadOptions = {
  /**
   * Custom lifecycle configuration for the uploaded file.
   * This object will be sent as the X-Fal-Object-Lifecycle header.
   */
  lifecycle?: StorageSettings;
}
```

Options for uploading a file.
