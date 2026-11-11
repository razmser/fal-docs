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

# MCP setup for Claude Code

> Add the fal MCP server to Claude Code, authenticate through your browser, and check the connection.

Add the fal MCP server to Claude Code, authenticate through your browser, and check the connection.

> [!TIP]
>
> To add fal to all supported agents on your computer at once, run the installer. See [Coding agents: one command](../mcp.md#coding-agents-one-command).
>
> ```bash theme={null}
> curl -fsSL https://fal.ai/mcp/install.sh | bash
> ```

## Setup

**1. Add fal**

Run this in your terminal:

```bash theme={null}
claude mcp add --transport http --scope user fal https://mcp.fal.ai/mcp-relay
```

This adds the connection across your projects. For project-only access, use `--scope project`; Claude Code saves that connection in `.mcp.json`.

**2. Sign in**

Open Claude Code, run `/mcp`, select `fal`, and choose **Authenticate**. Complete browser sign-in, then return to the terminal.

**3. Check tools**

Ask Claude Code to find a fal model without running it. If the tools are unavailable, start a new session.

See [Claude Code's MCP guide](https://code.claude.com/docs/en/mcp).

## Next steps

After connecting, [try your first generation](../mcp.md#try-your-first-generation). For account selection, tools, and troubleshooting, see the [fal MCP overview](../mcp.md).
