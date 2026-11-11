> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../documentation/quickstart.md) and the [Model APIs overview](../../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Add fal.ai to your Next.js app Integration

> Connect a Next.js app on Vercel to fal. Add the official fal integration from the Vercel marketplace, or set an API-scoped fal key as FAL_KEY by hand.

## You will learn how to:

* Connect a Next.js app deployed on Vercel to fal.ai

## Prerequisites

**Step 1**

A [fal.ai](https://fal.ai) account

**Step 2**

A [Vercel account](https://vercel.com)

**Step 3**

A Next.js app. Check the [Next.js guide](https://fal.ai/docs/model-apis/integrations/nextjs.md) if you don't have one yet.

**Step 4**

App deployed on Vercel. Run `npx vercel` in your app directory to deploy it in case you haven't done it yet.

## Vercel official integration

The recommended way to add fal.ai to your app deployed on Vercel is to use the official integration. You can find it in the [Vercel marketplace](https://vercel.com/integrations/fal).

Click on **Add integration** and follow the steps. After you're done, re-deploy your app and you're good to go!

![Vercel integration](https://integrations-og-image.vercel.sh/api/og/fal?42673700034a7509d66487f3ed68a2bd)

## Manual setup

You can also manually add fal credentials to your Vercel environment manually.

**Step 1**

Go to your [fal.ai dashboard](https://fal.ai/dashboard/keys), create an **API-scoped** key and copy it. Make sure you create an alias do identify which app is using it.

**Step 2**

Go to your app settings in Vercel and add a new environment variable called `FAL_KEY` with the value of the key you just copied. You can choose other names, but keep in mind that the default convention of fal-provided libraries is `FAL_KEY`.

**Step 3**

Re-deploy your app and you're good to go!
