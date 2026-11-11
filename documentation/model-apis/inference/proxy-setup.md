> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../quickstart.md) and the [Model APIs overview](../overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Proxy Setup

> Keep your API key secure in client-side applications

When the fal client runs in a browser, it cannot use your API key directly because browser source code is visible to anyone. Instead, it sends requests to a proxy endpoint on your server, which attaches the key and forwards the request to fal. The [Client Setup](client-setup.md#client-side-browser-react-next-js) page covers the quickest path using Next.js App Router. This page goes deeper with alternative frameworks, deployment platforms, and the underlying proxy protocol.

The proxy sits between the browser and fal's API. When the fal client makes a request, it sends it to your proxy URL (e.g., `/api/fal/proxy`) with the real fal endpoint in an `x-fal-target-url` header. Your proxy reads that header, adds your `FAL_KEY` via the `Authorization` header, forwards the request to fal, and pipes the response back to the browser. This means your key never leaves the server.

## Next.js App Router

Create `app/api/fal/proxy/route.ts`:

```typescript theme={null}
import { createRouteHandler } from "@fal-ai/server-proxy/nextjs";

export const { GET, POST, PUT } = createRouteHandler();
```

## Next.js Pages Router

If you are using the Pages Router instead of App Router, create `pages/api/fal/proxy.ts`:

```typescript theme={null}
import { createPageRouterHandler } from "@fal-ai/server-proxy/nextjs";

export default createPageRouterHandler();
```

The client configuration is the same for both routers:

```typescript theme={null}
import { fal } from "@fal-ai/client";

fal.config({
  proxyUrl: "/api/fal/proxy"
});
```

> [card] [Full Next.js Example](../../../examples/integrations/nextjs.md)
>
> Complete walkthrough with working code

> [card] [Vercel Integration](../../../examples/integrations/vercel.md)
>
> Deploy AI apps on Vercel with fal

## Proxy Configuration

Both `createRouteHandler()` and `createPageRouterHandler()` accept a `ProxyConfig` object to control security and access.

```typescript theme={null}
import { createRouteHandler } from "@fal-ai/server-proxy/nextjs";

export const { GET, POST, PUT } = createRouteHandler({
  allowedEndpoints: ["fal-ai/flux/**", "fal-ai/fast-sdxl"],
  allowUnauthorizedRequests: false,
});
```

| Option | Type | Default | Description |
| - | - | - | - |
| `allowedUrlPatterns` | `string[]` | `["fal.run/**", "queue.fal.run/**"]` | Glob patterns for allowed target URLs (without scheme). Add patterns like `"fal.ai/**"` if needed. |
| `allowedEndpoints` | `string[]` | `[]` (all allowed) | Glob patterns for allowed POST endpoints (path without leading slash, e.g. `"fal-ai/flux/**"`). An empty array allows all endpoints. |
| `allowUnauthorizedRequests` | `boolean` | `true` | Whether to allow requests without authentication. Set to `false` in production and provide `isAuthenticated`. |
| `isAuthenticated` | `(behavior) => Promise<boolean>` | Checks for `Authorization` header | Custom function to determine if the incoming request is authenticated. |
| `resolveFalAuth` | `(behavior) => Promise<string>` | Uses the `Authorization` header of the request, or the `FAL_KEY` env var if that header is not present | Custom function to resolve the fal API authorization value. |

> [!WARNING]
>
> The default configuration allows all endpoints and unauthenticated requests for backwards compatibility. For production, set `allowedEndpoints` to restrict which models can be called through your proxy and `allowUnauthorizedRequests: false` to require authentication.

> [!NOTE]
>
> The old `handler` and `route` exports from `@fal-ai/server-proxy/nextjs` are deprecated. Migrate to `createPageRouterHandler()` and `createRouteHandler()` respectively.

## Vercel

If you are deploying to Vercel, the Next.js proxy setup works out of the box. Set your `FAL_KEY` as an environment variable in your Vercel project settings (Settings > Environment Variables) and the proxy route will read it automatically. No additional configuration is needed beyond what is described in [Client Setup](client-setup.md#client-side-browser-react-next-js).

## Custom Proxy (Express, Flask, etc.)

If you are not using Next.js, you can implement the proxy with any framework. The proxy endpoint needs to:

1. Accept all HTTP methods (GET, POST, PUT, DELETE)
2. Read the target URL from the `x-fal-target-url` header
3. Add your API key via the `Authorization` header
4. Forward the request to fal and return the response

```javascript Express.js theme={null}
const express = require("express");
const fetch = require("node-fetch");

const app = express();
app.use(express.raw({ type: "*/*", limit: "50mb" }));

app.all("/api/fal/proxy", async (req, res) => {
  const targetUrl = req.headers["x-fal-target-url"];
  
  if (!targetUrl) {
    return res.status(400).json({ error: "Missing target URL" });
  }

  const response = await fetch(targetUrl, {
    method: req.method,
    headers: {
      ...req.headers,
      "Authorization": `Key ${process.env.FAL_KEY}`,
      "host": new URL(targetUrl).host
    },
    body: req.method !== "GET" ? req.body : undefined
  });

  res.status(response.status);
  response.headers.forEach((value, key) => res.setHeader(key, value));
  response.body.pipe(res);
});
```

Then configure the client in your frontend code to point at your proxy:

```javascript theme={null}
import { fal } from "@fal-ai/client";

fal.config({
  proxyUrl: "/api/fal/proxy"
});
```

> [!TIP]
>
> The proxy URL should be relative to your application root. The client automatically constructs the full URL based on the current origin.
