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

# Searching and filtering

> Search your assets by description, image, or video, and narrow the results with library filters.

## Search with a description

Enter a description in the search bar. For example:

```text theme={null}
a green glass bottle on a pale sandy background
```

Text search finds media by meaning and similarity. It is not an exact phrase or filename lookup. Describe the subject, setting, composition, or appearance you remember.

## Search with an image or video

Choose **Search by image** or **Search by video** and select an example file. You can also drop an image or video onto the search field. The library finds visually related media within the current scope.

Use one search input at a time. Clear the active example to return to typing a description.

## Narrow the results

Search stays within the section or collection you have open. Use the available filters to narrow it further:

| Filter | Example |
| :- | :- |
| Media type | Images or videos. |
| Source | Uploads or generated outputs. |
| Model | Outputs from a particular endpoint. |
| Tags | Assets with any or all of the selected tags. |
| Collection | Search inside a campaign's collection. |

For example, open **Summer campaign**, select **Images**, then search for `bottle on sand`. Clear the filters or return to **All media** if the scope is too narrow.

## Find characters and smart entities

In Agent, Sandbox, and supported model prompts, type `@` to open the entity picker. Search for a saved entity and select it as a reference.

The picker supports **characters, props, environments, styles, and scenes**. For example:

```text theme={null}
Create an image of @mara holding @green-bottle in @workshop.
Use @watercolor as the visual style.
```

You can combine several entity references in one prompt. Describe the role each entity plays in the result.

To browse an entity's associated media, open its page in Assets. See [Using entities in generations](../entities/using.md) for reference behavior and model requirements.

## Search freshness and large results

New generations and uploads can take time to become searchable. Browse recent media if an item is not yet returned by search.

If the library reports that a search scope is too large or results are incomplete, narrow it with a smaller collection or additional filters. See [FAQ and troubleshooting](../faq.md).

For a query you want to revisit, create a [smart collection](../organizing/smart-collections.md).
