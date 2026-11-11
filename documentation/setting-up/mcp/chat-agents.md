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

# MCP setup for chat agents

> Connect Muse, Instinct, and other agents that add MCP servers from chat.

Some agents add MCP servers from a chat message instead of a settings page. Muse and Instinct work this way. For Grok Bot, install the fal plugin instead. See [Grok Bot setup](grok-bot.md).

## Setup

**1. Ask the agent to add fal**

Paste this message into a new chat:

```text theme={null}
Add the fal MCP server: https://mcp.fal.ai/mcp-relay (remote, Streamable HTTP, OAuth sign-in).
```

If the agent asks for approval to add the server, approve it.

**2. Sign in**

Complete browser sign-in when the agent asks. Never paste an OAuth token or API key into the chat.

**3. Check tools**

Ask the agent to find a fal model without running it.

## Agent notes

> [tab] Muse
>
> Muse adds an MCP server that is not in its list as a [Custom Connector](https://www.meta.com/help/artificial-intelligence/1687253048996149/), which is the Muse name for it. Paste this message instead of the general one:
>
> ```text theme={null}
> Create a Custom Connector for fal. It is a remote MCP server at https://mcp.fal.ai/mcp-relay (Streamable HTTP, OAuth sign-in). Test it by listing its tools, then save it as a skill named fal.
> ```
>
> Muse adds the server, tests it, and saves it as a skill so that it stays available in later chats.
>
> If Muse asks for a key instead of browser sign-in, create a [fal API key](https://fal.ai/dashboard/keys). Enter it as the header `Authorization: Key <key_id>:<key_secret>` when Muse asks for it in its secure credentials form. Do not paste the key into the chat. A key connection uses the account that owns the key.
>
> Meta does not review custom connectors. In Muse Settings, choose when Muse asks for your approval before it uses fal.

> [tab] Instinct
>
> Instinct does not publish MCP setup instructions. If Instinct cannot add the server from chat, contact Instinct support.

## Next steps

After connecting, [try your first generation](../mcp.md#try-your-first-generation). For account selection, tools, and troubleshooting, see the [fal MCP overview](../mcp.md).
