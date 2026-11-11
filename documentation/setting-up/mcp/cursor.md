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

# MCP setup for Cursor

> Install the fal plugin from the Cursor Marketplace, or connect the fal MCP server with a remote server URL and browser sign-in.

Install the fal plugin from the Cursor Marketplace, or connect the fal MCP server with a remote server URL and browser sign-in.

> [!TIP]
>
> To add fal to all supported agents on your computer at once, run the installer. See [Coding agents: one command](../mcp.md#coding-agents-one-command).
>
> ```bash theme={null}
> curl -fsSL https://fal.ai/mcp/install.sh | bash
> ```

## Install from the Cursor Marketplace

The [fal plugin](https://cursor.com/marketplace/fal) adds the fal MCP server and fal skills for model selection, prompting, and media workflows.

**1. Add the plugin**

Open the [fal listing](https://cursor.com/marketplace/fal) in the Cursor Marketplace. Sign in to Cursor if prompted, then add the plugin.

**2. Sign in**

Open **Customize**, find fal in the MCP server list, and complete browser sign-in. Each user must authorize their own connection.

**3. Check tools**

Open an Agent chat and ask Cursor to find a fal model without running it.

## Manual setup

If you do not use the plugin, add the server to your MCP configuration.

**1. Add fal**

Open or create `~/.cursor/mcp.json` for access across your projects. Merge this entry into `mcpServers`, preserving your other connections:

```json theme={null}
{
  "mcpServers": {
    "fal": {
      "url": "https://mcp.fal.ai/mcp-relay"
    }
  }
}
```

For project-only access, use `.cursor/mcp.json` in your project instead.

**2. Sign in**

Open **Customize**, find fal in the MCP server list, and complete browser sign-in. Each user must authorize their own connection.

**3. Check tools**

Open an Agent chat and ask Cursor to find a fal model without running it.

See [Cursor's MCP guide](https://cursor.com/docs/mcp).

## Next steps

After connecting, [try your first generation](../mcp.md#try-your-first-generation). For account selection, tools, and troubleshooting, see the [fal MCP overview](../mcp.md).
