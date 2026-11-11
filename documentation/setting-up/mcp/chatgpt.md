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

# MCP setup for ChatGPT

> Install the fal plugin in ChatGPT, or connect the fal MCP server with developer mode.

Install the fal plugin in ChatGPT, or connect the fal MCP server with developer mode.

## Setup

**1. Install the plugin**

Open the [fal plugin listing](https://chatgpt.com/plugins/fal) and select **Install plugin**. Your workspace can require administrator approval.

**2. Connect fal**

Select **Connect** for the fal app if prompted. Complete browser sign-in and authorize the connection.

**3. Check tools**

In a new chat, type `@fal`. Ask ChatGPT to find an image-generation model without running it.

See the [fal plugin guide](../codex-plugin.md) and [OpenAI's plugin instructions](https://help.openai.com/en/articles/20001256-plugins-in-chatgpt).

> [accordion] Manual setup with developer mode
>
> If you need a custom MCP app instead of the plugin, use [ChatGPT developer mode](https://help.openai.com/en/articles/12584461-developer-mode-and-mcp-apps-in-chatgpt). Your workspace must permit custom apps. Full MCP actions require Business or Enterprise/Edu; Pro custom MCP apps support read-only tools.
>
> Open **Settings → Apps → Create**. Enter `https://mcp.fal.ai/mcp-relay` as the server URL, select **OAuth**, and complete browser sign-in. Scan the tools. A read-only custom app cannot run generations.

## Next steps

After connecting, [try your first generation](../mcp.md#try-your-first-generation). For account selection, tools, and troubleshooting, see the [fal MCP overview](../mcp.md).
