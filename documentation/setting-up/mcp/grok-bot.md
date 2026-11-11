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

# MCP setup for Grok Bot

> Add the fal plugin to Grok Bot and connect your fal account with browser sign-in.

Add the fal plugin to Grok Bot and connect your fal account with browser sign-in.

The plugin adds the fal MCP server and fal skills for model selection, prompting, and media workflows. Grok Bot uses the Cursor plugin marketplace, so this is the same plugin as the [Cursor plugin](cursor.md).

## Setup

**1. Add the plugin**

Open the [fal for Grok Bot](https://x.ai/bot/plugin/78546892) plugin page and select **Add to Grok Bot**.

**2. Sign in**

Complete browser sign-in with your fal account when Grok Bot asks you to connect fal. You do not need an API key.

**3. Check tools**

Ask Grok Bot to find a fal image-generation model without running it.

On grok.com, add fal from the connector settings instead. Open **Connectors**, select **New Connector**, then select **Custom**. Paste `https://mcp.fal.ai/mcp-relay` and complete browser sign-in. See [xAI's connector guide](https://docs.x.ai/grok/connectors).

## Next steps

After connecting, [try your first generation](../mcp.md#try-your-first-generation). For account selection, tools, and troubleshooting, see the [fal MCP overview](../mcp.md).
