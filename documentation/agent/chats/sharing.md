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

# Sharing and forking

> Share a read-only view of a chat with a link, restrict the audience, set an expiry, and fork a chat from any message.

## Share a chat

Click **Share** in the chat header, or choose **Share chat** from a message's actions. fal Agent creates a share link at `fal.ai/chat/share/<id>`. Viewers see a read-only copy of the chat with its generations.

![The Share chat dialog with the share link, the Base access selector, and the Link expires selector](https://mintcdn.com/fal-d8505a2e/DmEWlE6WV9vaz0Me/images/agent/share-dialog.png?fit=max\&auto=format\&n=DmEWlE6WV9vaz0Me\&q=85\&s=fb6cb372c6fa8dfd6961b0aca8b70189)

### Base access

| Option | Who can open the link |
| :- | :- |
| **Everyone with link** | Anyone |
| **Team** | Members of your team, or specific email addresses |
| **Org** | Members of your organization, or specific email addresses |
| **Restricted** | Only the people you list |

### Expiry

Set the link to expire **Never**, **In 1 day**, **In 7 days**, or **In 30 days**. You can revoke a share at any time from the share dialog.

## Fork a chat

Hover a message from the agent to show its actions. Choose **Fork from here**. A new chat starts with the history up to that message. The original chat is unchanged. Use a fork to try a different direction without losing the current thread.

![Message actions under an agent reply: copy, thumbs up, thumbs down, Fork from here, and share](https://mintcdn.com/fal-d8505a2e/DmEWlE6WV9vaz0Me/images/agent/fork-from-here.png?fit=max\&auto=format\&n=DmEWlE6WV9vaz0Me\&q=85\&s=753f35ba49b293288c0dba4e15267da0)

The same row holds **Copy**, feedback buttons, and **Share chat**.

## Examples

fal publishes a few example chats at `fal.ai/agent/example/<slug>`. They are visible to visitors who are signed out or do not have access yet, and show what a full session looks like.
