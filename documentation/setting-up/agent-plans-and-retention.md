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

# fal Agent Plans and Retention

> Understand default fal Agent access, optional credit-plan benefits, and Enterprise chat-retention controls.

Access is enabled by default for signed-in accounts. You do not need a credit plan or an Enterprise contract to use fal Agent. Account and organization restrictions still apply.

Credit plan benefits and retention settings depend on the account selected in fal. Before changing them, confirm that you are using the intended personal account or workspace.

## Optional Credit Plans

Starter, Pro, and Max are optional monthly credit plans for eligible personal accounts with billing access. They provide credits and, for Pro and Max, usage discounts. They do not determine Agent access.

| Plan | Price | Monthly credits | Usage discount |
| - | -: | -: | -: |
| **fal Agent Starter** | \$50 / month | \$50 | No discount |
| **fal Agent Pro** | \$200 / month | \$200 | 5% |
| **fal Agent Max** | \$1,000 / month | \$1,000 | 10% |

Credits and any applicable usage discount begin after the first payment succeeds. If you cancel, plan benefits continue through the end of the paid billing period. Previously purchased credits are not affected. Canceling a plan does not remove Agent access.

> [!NOTE]
>
> If fal Agent shows an access-denied message, contact your organization
> administrator or fal account team. Buying a credit plan does not override an
> account or organization restriction.

Manage your plan and payment status from [Billing](https://fal.ai/dashboard/billing).

## Where the Credit-Plan Discount Applies

The Pro and Max discounts apply to usage through the fal UI, Sandbox, Playground, and CLI. This includes Partner API models used through those surfaces. Direct API usage remains pay as you go and does not receive the credit-plan discount.

Monthly credits from your credit plan can be used across fal products and are spent before pay-as-you-go charges.

## Configure chat retention

Enterprise organizations and their workspaces can control how long fal Agent prompts, generations, uploads, and generated media are retained. The default is **Forever**. Finite retention options range from **1 hour** to **1 year**.

Organization and workspace administrators can change the setting under [Agent settings](https://fal.ai/agent/settings). A workspace inherits its organization's retention period unless an administrator sets a workspace-specific value.

The active retention period is applied to each new chat turn and its associated files. When a turn expires, its prompts, responses, and media are removed. After every turn in a chat expires, the conversation is renamed **Expired chat**.

> [!WARNING]
>
> Choosing a finite retention period disables durable project memory. Agent can
> search project content that is still inside the retention window, but it
> cannot save durable project notes or distill new notes from chats. If you ask
> Agent to remember something, it will report that the note was not stored.

Choose **Forever** if your workflows depend on durable project notes across chats.
