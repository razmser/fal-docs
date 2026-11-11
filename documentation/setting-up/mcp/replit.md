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

# MCP setup for Replit

> Connect Replit Agent to the fal MCP server with an install link and browser sign-in.

Connect Replit Agent to the fal MCP server with an install link and browser sign-in.

## Setup

**1. Add fal**

Open the [fal install link for Replit](https://replit.com/integrations?mcp=eyJkaXNwbGF5TmFtZSI6ImZhbCIsImJhc2VVcmwiOiJodHRwczovL21jcC5mYWwuYWkvbWNwLXJlbGF5In0%3D). Replit opens **Integrations** with the fal server URL filled in. If Replit asks you to log in first, open the link again after you log in.

To add the server manually, open **MCP Servers** and select **Add MCP server**. Enter `fal` as the name and `https://mcp.fal.ai/mcp-relay` as the URL.

**2. Sign in**

Select **Test & save**. Complete browser sign-in, then return to Replit.

**3. Check tools**

Ask Replit Agent to find a fal model without running it.

See [Replit's MCP guide](https://docs.replit.com/build/connect-via-mcp).

## Next steps

After connecting, [try your first generation](../mcp.md#try-your-first-generation). For account selection, tools, and troubleshooting, see the [fal MCP overview](../mcp.md).
