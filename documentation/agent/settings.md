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

# Settings

> Every fal Agent preference: profile, default model, sequencer, notifications, skills, connectors, spending caps, and retention.

Open **Settings** from the sidebar. Changes save with the **Save changes** button on each tab.

## General

### Profile

| Setting | Effect |
| :- | :- |
| **What should fal Agent call you?** | The name the agent uses. Leave blank to use your account name |
| **What best describes your work?** | Helps the agent tailor its responses. Options include Design, Marketing, Engineering, Product management, and others |

### Chat

| Setting | Effect |
| :- | :- |
| **Default model** | The language model that powers the agent's reasoning. The recommended model is selected by default. Other options carry a **Recommended** or **Experimental** badge. Experimental models can be faster but less reliable on long tasks |
| **Enable sequencer** | Turns on [video sequences](tools/video-sequences.md). When off, the agent renders combined videos to a file instead |

### Chat retention

Shown for enterprise accounts and teams under an organization. Sets how long prompts and generations stay in the workspace. See [Retention](retention.md).

## Notifications

**Notify me when a turn finishes.** A browser notification fires when a turn completes while the tab is hidden. Click it to jump back to the chat. On by default.

## Skills

**Enable skills.** The master switch for [skills](skills.md). When off, the agent ignores all skills, including fal's and yours. Manage individual skills on the Skills page.

## Connectors

Connect and manage external apps. See [Connectors](tools/connectors.md). This tab appears only for accounts with connector access.

## Spending caps

**Confirm runs above** sets the safety cap in USD, and the media-type toggles decide which runs ask for confirmation. See [Spending caps](spending-caps.md).
