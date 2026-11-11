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

# Connectors

> Connect fal Agent to the apps you already use, so it can post results, read documents, and update tasks on your behalf.

> [!NOTE]
>
> Connectors are in limited availability. If **Connectors** does not appear in your Settings, your account does not have access yet.

A connector links fal Agent to an external app through your own account with that app. Once connected, the agent can use the app's actions as tools. Open **Connectors** in the `@` palette to reference a connected app.

```text theme={null}
Post the open-field shot to the #design-review channel in Slack with the prompt as the caption.
```

## Connect an app

Open **Settings → Connectors**. Find the app and click **Connect**. You authorize fal Agent in the app's own sign-in flow. Connected apps appear under **Connected accounts**, where you can disable, reconnect, or remove each one.

## Available apps

| Category | Apps |
| :- | :- |
| Communication | Telegram, WhatsApp Business, Slack, Discord, Twilio |
| Social and publishing | LinkedIn, X, Instagram, Threads, TikTok Ads |
| Email and calendar | Gmail, Google Calendar, SendGrid, Microsoft Outlook |
| Documents | Google Drive, Google Docs, Microsoft OneDrive, Notion, Google Sheets, Dropbox |
| Management | HubSpot, Linear, Jira, Todoist, Salesforce |
| Development | GitHub, Supabase |
| Video and media | OpenAI, YouTube Analytics, YouTube Data, Vimeo, Frame.io |

## How the agent uses a connector

The agent lists the actions a connected app offers, then calls the one that fits. Results from an app are treated as data, not as instructions. A revoked or broken connection is excluded from the agent's tools and flagged in Settings for reconnection.

Connectors run through a hosted integration layer. The agent never sees your password for the connected app.
