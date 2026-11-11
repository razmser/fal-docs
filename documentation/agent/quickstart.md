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

# Quickstart

> Start your first fal Agent chat, attach a reference, review the plan, and download the result.

This guide takes you from sign-in to a downloaded generation. It takes about five minutes.

## Before you start

You need a fal account. Access is enabled by default for signed-in accounts. You do not need a credit plan or an Enterprise contract to use fal Agent. Account and organization restrictions still apply. Model runs are billed as normal fal usage. See [Access and pricing](access-and-pricing.md) for details.

**1. Open fal Agent**

Go to [fal.ai/agent](https://fal.ai/agent). Sign in with Google, GitHub, or SSO. If prompted, complete account setup. Prompts entered before sign-in are saved so you can continue after sign-in, if your account has access.

If access is disabled for your account or organization, contact your organization administrator or fal support. Buying a credit plan does not override an access restriction.

**2. Describe what you want**

Type a request in the composer. Be specific about the subject, the format, and the number of outputs. For example:

```text theme={null}
Make three product hero shots of this bottle on a marble surface.
Soft morning light, 4:5 aspect ratio. Then animate the best one.
```

Press `Enter` to send. `Shift+Enter` adds a new line.

![A new chat with starter cards and the composer](https://mintcdn.com/fal-d8505a2e/DmEWlE6WV9vaz0Me/images/agent/new-chat.jpg?fit=max\&auto=format\&n=DmEWlE6WV9vaz0Me\&q=85\&s=56c4366fa9623988d6bcf2c61fdf376e)

**3. Attach a reference**

Drag an image, a video, or a document into the chat, or click the attachment button. The agent uses attachments as references, edit sources, or context. See [Composer and references](chats/composer.md) for the supported file types and limits.

**4. Review the plan**

For a multi-step request, the agent shows a plan card before it runs anything. Read the steps and the model chips. You can rename a step, reorder steps, pin a different model, or add an approval checkpoint. Click **Run plan** when the plan looks right. Simple requests run directly without a plan card.

**5. Watch the generations land**

Each run appears as a cell in the chat and in the media rail on the right. In-flight cells show a cancel button. When a run finishes, hover the cell for **Retry** and **Download**. Failed runs show a short explanation, and the agent repairs the input and retries when it can.

**6. Iterate**

Type `@` to open the palette. Pick a previous output under **Generations**, or pin a model under **Models**. The agent keeps the chat context, so you can say "make the second one warmer" without repeating the prompt.

**7. Download or export**

Click **Download** on a cell to save one file. Ask the agent to "export everything as a zip" to get one archive with a folder per batch and a manifest. See [Export](generations/export.md).

## Next steps

> [card] [Chat interface](chats/interface.md)
>
> Sidebar, media rail, history, shortcuts, and notifications

> [card] [Models and generation](models-and-generation.md)
>
> How the agent chooses models and repairs failed runs

> [card] [Projects](projects.md)
>
> Group chats and media under a shared memory

> [card] [Spending caps](spending-caps.md)
>
> Confirm expensive runs before they start
