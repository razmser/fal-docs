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

# Access and pricing

> How to access fal Agent, what optional credit plans include, and what fal bills for.

fal Agent is in Early Access. Access is enabled by default for signed-in accounts. You do not need a credit plan or an Enterprise contract to use fal Agent. Account and organization restrictions still apply.

Sign in at [fal.ai/agent](https://fal.ai/agent). If prompted, complete account setup.

## Optional Credit Plans

Credit plans provide monthly credits. They are not required for Agent access. The credits work across every fal product. fal spends them before pay-as-you-go rates, and unused credits roll over each month.

| Plan | Price | Monthly credits | Discount |
| :- | :- | :- | :- |
| **fal Agent Starter** | \$50 / month | \$50 | No discount |
| **fal Agent Pro** | \$200 / month | \$200 | 5% off UI, Sandbox, Playground, and CLI usage |
| **fal Agent Max** | \$1,000 / month | \$1,000 | 10% off UI, Sandbox, Playground, and CLI usage |
| **Enterprise** | Custom | Custom volume | Custom rates |

You can cancel at any time. Plan benefits continue until the end of the paid period. Previously purchased credits are not affected. Canceling a plan does not remove Agent access. The discount does not apply to API usage, which is always pay-as-you-go.

> [!NOTE]
>
> Self-service credit plans are for eligible personal accounts with billing access.
>
> * Team or organization accounts are ineligible.
> * Enterprise accounts are ineligible.
> * Invoiced accounts are ineligible.
> * Accounts billed through a parent account are ineligible.
> * Accounts billed through GCP are ineligible.
> * Accounts billed through Vercel are ineligible.
> * Active pricing agreements can prevent credit plan purchases.
>
> These are billing restrictions, not Agent access requirements.

## What fal bills for

fal bills **model runs only**. Every generation the agent submits is a normal fal model run. It is charged at the model's rate against your credits, then at pay-as-you-go rates.

Everything else in fal Agent is free to use. This includes the agent's reasoning, the sandbox, web search and URL reading, and video understanding. fal covers these costs for now.

The spend chip next to the composer shows the running cost of the chat, split by model and by media type. [Spending caps](spending-caps.md) ask you to confirm runs above a threshold.

## Model access

The agent can only run models that your account can call through the API. If your organization restricts a model, the agent does not see it or suggest it. It also refuses to run the model when you mention it directly. See [Access controls](../organizations/access-controls.md).

## Complimentary credits

fal gives select filmmakers, creators, studios, and agencies complimentary credits to build with fal Agent. Selected applicants receive up to \$1,000 in credits. Applications are reviewed on a rolling basis, and selection is not guaranteed. Apply at [fal.ai/agent-access/claim](https://fal.ai/agent-access/claim).

## What happens without access

Signed-out visitors see the fal Agent landing page with a live composer. Prompts entered before signing in are saved so you can continue after sign-in, if your account has access.

If access is disabled for your account or organization, fal Agent shows an access-denied message. Contact your organization administrator or fal support. Buying a credit plan does not override an access restriction.
