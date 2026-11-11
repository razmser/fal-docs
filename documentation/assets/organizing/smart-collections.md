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

# Smart collections

> Save rules that automatically find matching media in your library.

A smart collection saves a set of rules. When you open it, Assets finds media that matches those rules. You do not add individual assets to it by hand.

## Create a smart collection

Open **New smart collection**, give it a name, and add the conditions you want. Review the preview matches, then choose **Create smart collection**.

For example, create **Recent product outputs** with a generated-output source, a model endpoint, and a creation date on or after the start of your campaign.

## Available rules

| Rule | What you can specify |
| :- | :- |
| Endpoint | A model endpoint, including equality, exclusion, or a list of endpoints. |
| Source | Generated output or upload. |
| Created at | Dates before or after a boundary, including inclusive boundaries. |
| Semantic match | A text description, image URL, or video URL, with a similarity threshold. |

Combine ordinary conditions with **Match All** or **Match Any**, and use groups for related conditions.

## Similarity rules

A smart collection supports **one similarity rule**. Use it to define the visual theme, then use ordinary conditions to narrow the results. For example, match `minimal product photography on sand` and restrict the source to generated outputs.

Preview the matches while adjusting the rule. A stricter similarity threshold reduces loosely related results. Search inside the collection to further narrow its matching media.

## Edit and organize

Open **Edit smart collection** to change its name, description, or rules. Results reflect the updated rules when queried.

Smart collections are separate from manual collections:

* You cannot manually add assets to a smart collection.
* Uploading while viewing one does not guarantee the upload matches its rules.
* Smart collections cannot be nested in folders.
* A manual collection cannot be converted into a smart collection, or vice versa. Create a new collection of the desired kind.

Deleting a smart collection removes the saved rules, not the matching media.
