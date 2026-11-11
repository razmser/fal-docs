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

# Using entities in generations

> Reference characters, props, environments, styles, and scenes in Agent and supported model prompts.

## In fal Agent

Type `@` in the composer to search for a saved entity. Select a character, prop, environment, style, or scene. Describe what you want to make with those references:

```text theme={null}
Create a product hero shot of @green-bottle in @workshop.
Use @watercolor as the visual style.
```

The agent receives the selected entities as context for the generation. Be explicit about the role each must play, especially when combining a character, prop, environment, and style.

You can also create an entity from reference images as part of an Agent workflow. See [Assets in Agent](../../agent/assets-and-characters.md).

## In Sandbox and supported model prompts

In Sandbox and supported model prompts, type `@` to search characters, props, environments, styles, and scenes. Select the entity, then review the model's inputs before generating.

How the reference is used depends on the model:

* A model with a supported image-reference input can receive reference images.
* A text-only model can use an entity's description in the prompt, but cannot receive its reference images.
* An entity without a description cannot be used on a text-only model through this flow. The form blocks an unsupported run.

The number of images a model accepts can be lower than the entity's twenty-reference limit. Select a model appropriate for the edit or reference task rather than assuming every model uses every reference.

## Reference images and gallery links

Only the entity's defining references establish its reference set. Manually linking a new output to the entity's gallery does not automatically add it to the reference images. Edit the entity to make that change.

## Find the results

Open the entity's page to browse associated media, or inspect a result's [generation details](../library/history.md).

See [Searching and filtering](../library/search.md) for finding library media and saved entities.
