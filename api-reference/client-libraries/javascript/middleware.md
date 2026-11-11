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

# middleware

> API reference for @fal-ai/client middleware

***

## Functions

### withMiddleware

```typescript theme={null}
function withMiddleware(middlewares: RequestMiddleware[]): RequestMiddleware
```

Setup a execution chain of middleware functions.

| Parameter | Type | Description |
| :- | :- | :- |
| `middlewares` | `RequestMiddleware[]` | one or more middleware functions. |

**Returns:** `RequestMiddleware`

### withProxy

```typescript theme={null}
function withProxy(config: RequestProxyConfig): RequestMiddleware
```

| Parameter | Type | Description |
| :- | :- | :- |
| `config` | `RequestProxyConfig` | - |

**Returns:** `RequestMiddleware`

***

## Types

### RequestConfig

```typescript theme={null}
type RequestConfig = {
  url: string;
  method: string;
  headers?: Record<string, string | string[]>;
}
```

A request configuration object.

**Note:** This is a simplified version of the `RequestConfig` type from the
`fetch` API. It contains only the properties that are relevant for the
fal client. It also works around the fact that the `fetch` API `Request`
does not support mutability, its clone method has critical limitations
to our use case.

### RequestMiddleware

```typescript theme={null}
type RequestMiddleware = (
  request: RequestConfig,
) => Promise<RequestConfig>
```

### ProxyRuntimeEnv

```typescript theme={null}
type ProxyRuntimeEnv = {
  isBrowser: boolean;
}
```

Runtime information passed to a user-provided
predicate so it can decide whether the proxy middleware should apply.

### ProxyRuntimeGate

```typescript theme={null}
type ProxyRuntimeGate = | "browser"
  | "always"
  | ((env: ProxyRuntimeEnv) => boolean)
```

Controls when the proxy middleware applies:

* `"browser"` — only when a DOM `window` is available (default).
* `"always"` — in every runtime (Node, Electron, Bun, workers, etc.).
* A predicate receiving  — full control per call.

### RequestProxyConfig

```typescript theme={null}
type RequestProxyConfig = {
  targetUrl: string;
  when?: ProxyRuntimeGate;
}
```
