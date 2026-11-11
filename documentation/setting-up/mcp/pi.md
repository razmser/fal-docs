> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../quickstart.md) and the [Model APIs overview](../../model-apis/overview.md).
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

# MCP setup for Pi

> Connect Pi to the fal MCP server using OAuth, without an API key.

Connect Pi to the fal MCP server using OAuth, without an API key.

## Setup

**1. Add fal**

Run this in your terminal:

```bash theme={null}
pi mcp add fal --url https://mcp.fal.ai/mcp-relay
```

Pi saves the connection in `~/.pi/agent/mcp.json` for use across your projects. Add `--local` to save it in `.pi/mcp.json` for the current project instead.

**2. Sign in**

Run the login command. Complete sign-in in your browser.

```bash theme={null}
pi mcp login fal
```

**3. Check the connection**

Run this in your terminal:

```bash theme={null}
pi mcp list
```

Confirm that `fal` is connected. If Pi reports `needs sign-in`, run the login command again.

**4. Check tools**

Start a new Pi session. For an existing session, run `/reload` to load the connection.

Run `/mcp` to inspect the server. Ask Pi to find a fal model without running it.

See [Pi's MCP guide](https://github.com/earendil-works/pi/blob/main/packages/coding-agent/docs/mcp.md).

## Next steps

After connecting, [try your first generation](../mcp.md#try-your-first-generation). For account selection, tools, and troubleshooting, see the [fal MCP overview](../mcp.md).
