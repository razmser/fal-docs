> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# fal for ChatGPT and Codex

> Generate and edit images, video, audio, and 3D assets in ChatGPT and Codex with the fal plugin.

Use the fal plugin to generate and edit images, video, audio, and 3D assets in
ChatGPT and Codex. It connects your fal account and includes skills for model selection,
prompting, and media workflows.

## Setup

1. Open the [fal plugin listing](https://chatgpt.com/plugins/fal) in the shared ChatGPT and Codex plugin directory.
2. Install the plugin.
3. Select **Connect** for the fal app if prompted. Complete browser sign-in and authorize the connection.
4. Start a new chat. In ChatGPT, type `@fal`, then ask the assistant to find a model without running it. In Codex, start a new session and ask it to use fal.

For ChatGPT connection details, see [OpenAI's plugin instructions](https://help.openai.com/en/articles/20001256-plugins-in-chatgpt).

In Codex CLI, use `/plugins` to find and install fal, then start a new session.
The Codex IDE extension does not support plugins. See
[OpenAI's plugin guide](https://learn.chatgpt.com/docs/plugins) for supported apps
and installation details.

If fal isn't available in your plugin directory, use the
[Run MCP setup](mcp.md) with a compatible MCP client.

To check the connection, ask: "Use fal to search for image generation models. Do not run a model."
The assistant should return model search results.

> [!NOTE]
>
> OAuth uses your personal fal account by default. To change it, select **Active MCP account** in [MCP & connectors](https://fal.ai/dashboard/ai-tools). You can also open
> [Account settings](https://fal.ai/dashboard/account/details), open the desired
> account's row menu, and choose **Use for MCP**. Future operations across all your
> OAuth MCP connections use that account immediately. No reconnect or plugin
> reinstall is needed. The website's account switcher is separate.
>
> Existing jobs stay on the account that started them. MCP responses show which
> account was used. If access to the selected account fails, operations stop
> instead of using personal credits. API-key connections still use their key's
> account. Ask your assistant to check pricing before generating.

## Examples

### Start with an image

> Use fal to create a studio product photo of a ceramic coffee cup on a pale blue background. Choose a suitable model. Show the estimated cost. Ask for approval before generation. After approval, wait for completion. Return the image with a download link.

To use a specific model, name it in your request.

### Edit a reference

> Use fal to turn this product photo into a summer campaign image. Keep the product shape, label, and colors unchanged.

Provide a public image URL, or attach the image if your app lets the assistant access its bytes. See
[file uploads](mcp.md#upload_file) for supported inputs.

### Turn an image into video

> Use fal to animate this image into a five-second product video with a slow camera push-in. Keep the product consistent with the source image.

The assistant submits the video to the queue, checks its status, and retrieves the output.

### Build a set of assets

> Use fal to design a coffee brand called Daybreak: a product photo, three social posts, and a short launch video. Keep the palette and packaging consistent across the set.

## Related Tools

Use [Run MCP](mcp.md) to access fal's tools from other MCP
clients. The plugin adds media workflow skills to those tools.

For logs, deployments, and serverless debugging, connect the separate
[Platform MCP](platform-mcp.md).

## Troubleshooting

* **Your credits are on a team account:** select **Active MCP account** in [MCP & connectors](https://fal.ai/dashboard/ai-tools), or open [Account settings](https://fal.ai/dashboard/account/details) and choose **Use for MCP** from the team's row menu. Switching accounts on the website alone does not change the account used by the plugin.
* **Tools aren't available:** confirm the plugin is installed and its fal connection is authorized, then start a new conversation.
* **An upload fails:** the hosted server cannot read local paths. Use a public file URL or upload the file as described in [file uploads](mcp.md#upload_file).
* **A job is still processing or a tool call times out:** ask your assistant to check the existing request ID. Submitting again creates another billable job.
* **A model rejects the request:** ask your assistant to check the current model schema and the reported input errors before trying again.
