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

# config

> API reference for @fal-ai/client config

***

## Functions

### resolveDefaultFetch

```typescript theme={null}
function resolveDefaultFetch(): FetchType
```

**Returns:** `FetchType`

### createConfig

```typescript theme={null}
function createConfig(config: Config): RequiredConfig
```

Configures the fal client.

| Parameter | Type | Description |
| :- | :- | :- |
| `config` | `Config` | the new configuration. |

**Returns:** `RequiredConfig`

### getRestApiUrl

```typescript theme={null}
function getRestApiUrl(): string
```

**Returns:** `string`

***

## Types

### CredentialsResolver

```typescript theme={null}
type CredentialsResolver = () => string | undefined
```

### ProxyUrlConfig

```typescript theme={null}
type ProxyUrlConfig = {
  /**
   * The URL of the proxy server. The proxy server should forward the
   * requests to the fal api.
   */
  url: string;
  /**
   * When the proxy middleware should apply. Defaults to `"browser"`.
   * @see ProxyRuntimeGate
   */
  when?: ProxyRuntimeGate;
}
```

Full configuration for the proxy middleware. Use this object form of
`proxyUrl` to control when the proxy applies in non-browser runtimes.

### Config

````typescript theme={null}
type Config = {
  /**
   * The credentials to use for the fal client. When using the
   * client in the browser, it's recommended to use a proxy server to avoid
   * exposing the credentials in the client's environment.
   *
   * By default it tries to use the `FAL_KEY` environment variable, when
   * `process.env` is defined.
   *
   * @see https://fal.ai/docs/model-endpoints/server-side
   * @see #suppressLocalCredentialsWarning
   */
  credentials?: undefined | string | CredentialsResolver;
  /**
   * Suppresses the warning when the fal credentials are exposed in the
   * browser's environment. Make sure you understand the security implications
   * before enabling this option.
   */
  suppressLocalCredentialsWarning?: boolean;
  /**
   * The proxy server to use for the client requests. The proxy server should
   * forward the requests to the fal api.
   *
   * Pass a `string` for the common browser-only case — the middleware rewrites
   * requests to this URL when a DOM `window` is available and does nothing
   * otherwise (the default, recommended for standard web apps).
   *
   * Pass a {@link ProxyUrlConfig} object to opt into non-browser runtimes
   * (Electron, server, edge/worker, Bun, etc.) via the `when` gate.
   *
   * @example Browser-only (unchanged behavior):
   * ```ts
   * fal.config({ proxyUrl: "/api/fal/proxy" });
   * ```
   *
   * @example Always apply, regardless of runtime:
   * ```ts
   * fal.config({
   *   proxyUrl: { url: "/api/fal/proxy", when: "always" },
   * });
   * ```
   *
   * @example Custom predicate (e.g. Electron renderer only):
   * ```ts
   * fal.config({
   *   proxyUrl: {
   *     url: "/api/fal/proxy",
   *     when: ({ isBrowser }) => isBrowser || process.type === "renderer",
   *   },
   * });
   * ```
   */
  proxyUrl?: string | ProxyUrlConfig;
  /**
   * The request middleware to use for the client requests. By default it
   * doesn't apply any middleware.
   */
  requestMiddleware?: RequestMiddleware;
  /**
   * The response handler to use for the client requests. By default it uses
   * a built-in response handler that returns the JSON response.
   */
  responseHandler?: ResponseHandler<any>;
  /**
   * The fetch implementation to use for the client requests. By default it uses
   * the global `fetch` function.
   */
  fetch?: FetchType;
  /**
   * Retry configuration for handling transient errors like rate limiting and server errors.
   * When not specified, a default retry configuration is used.
   */
  retry?: Partial<RetryOptions>;
}
````

### RequiredConfig

```typescript theme={null}
type RequiredConfig = Required<Config>
```
