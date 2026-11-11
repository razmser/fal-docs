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

# FAQ

> Common questions about fal Agent access, models, cost, limits, and programmatic use.

> [accordion] Which models can the agent use?
>
> Every model in the fal catalog that your account can call. The catalog refreshes every few minutes. Restricted models are hidden from the agent and cannot be run by mention. See [Models and generation](models-and-generation.md).

> [accordion] Can I choose the model myself?
>
> Yes. Type `@` in the composer, open **Models**, and pick one. You can also pin a model on a plan card step. A pinned model is final. Without a pin, the agent chooses.

> [accordion] How much does a chat cost?
>
> You pay for model runs at normal fal rates against your credits. Nothing else is billed. The agent's reasoning, the sandbox, web search, and video understanding are free to use for now. The spend chip next to the composer shows the running total. See [Access and pricing](access-and-pricing.md).

> [accordion] How do I stop the agent from spending too much?
>
> Set a safety cap in **Settings → Spending caps**. Runs above the cap ask for confirmation. Videos and audio ask by default. See [Spending caps](spending-caps.md).

> [accordion] Why did the agent stop in the middle of a task?
>
> A turn has a step limit of about 60 tool calls. Long tasks pause with a message. Send another message to continue. A plan checkpoint or a cost approval also pauses the run until you approve.

> [accordion] Can the agent edit my image without regenerating it?
>
> Yes. Pixel-exact edits such as crop, resize, brightness, background removal, and captions run as code in the [sandbox](tools/sandbox.md). Mark the region in the [media viewer](generations/media-viewer.md) to point at the part to change.

> [accordion] Where do my files go?
>
> Every input and output is saved to your fal Assets library and linked to its chat. See [Assets and characters](assets-and-characters.md) and [Retention](retention.md).

> [accordion] Can my team share a project?
>
> Chats can be shared with your team or organization as read-only links. See [Sharing and forking](chats/sharing.md). Shared editable projects are not available yet.

> [accordion] Is there an API or SDK for fal Agent?
>
> Not yet. fal Agent is available in the browser at [fal.ai/agent](https://fal.ai/agent). For programmatic generation today, use the [Model APIs](../model-apis/overview.md), the [MCP server](../setting-up/mcp.md), or the [genmedia CLI](../setting-up/genmedia.md).

> [accordion] Does fal Agent work on mobile?
>
> Yes. The site has a mobile layout, and you can install it as an app from your browser. See [Chat interface](chats/interface.md#install-as-an-app).

> [accordion] What file types can I attach?
>
> Images, video, audio, documents, common code and text formats, and ZIP archives for training data. See the full table in [Composer and references](chats/composer.md#attachments).

> [accordion] How do I get help?
>
> Use the [fal Discord](https://discord.gg/fal-ai) or contact [support](../model-apis/support.md). Include the chat link and the request ID from the tile's details when a run fails.
