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

# MCP setup for Claude

> Connect Claude to the fal MCP server with browser sign-in, then find models and generate media.

Connect Claude to the fal MCP server with browser sign-in, then find models and generate media.

## Setup

**1. Open connectors**

Open **Customize → Connectors**. Select **Add**, then **Add custom connector**.

**2. Add fal**

Enter `fal` as the name and `https://mcp.fal.ai/mcp-relay` as the remote MCP server URL. Select **Continue**.

**3. Sign in**

Review the OAuth settings and select **Continue**. Choose **Sign in now**, then **Register automatically** and **Add**. Complete browser sign-in and authorization.

**4. Enable fal**

In a new chat, open the **+** menu, select **Connectors**, and enable fal. Ask Claude to find a model without running it.

On Team or Enterprise, ask your workspace owner to add fal in **Organization settings → Connectors**. Enterprise members with connector-management permission can also add it. Members then connect through **Customize → Connectors**.

See [Claude's custom connector guide](https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp) for availability and organization permissions.

## Next steps

After connecting, [try your first generation](../mcp.md#try-your-first-generation). For account selection, tools, and troubleshooting, see the [fal MCP overview](../mcp.md).
