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

# FAQ and troubleshooting

> Resolve missing assets, search issues, unavailable references, sharing restrictions, and download failures.

> [accordion] Why is a generation missing?
>
> Check that the generation completed. Select the account that ran it. Check that the account's Assets configuration includes its request source. Clear filters and look in Generated media. Deleted or expired items can become unavailable. See [Adding assets](library/adding.md).

> [accordion] Why can I browse an asset but not find it in search?
>
> New assets can become browsable before they are searchable. Try again later and check the current section, collection, and filters. Text search is semantic, so try a description of the visual content rather than expecting an exact filename or phrase match.

> [accordion] Which entity types can I search for with @?
>
> Agent, Sandbox, and supported model prompts let you search and select characters, props, environments, styles, and scenes. Type `@` to open the picker. Open an entity's page in Assets to browse its associated media.

> [accordion] Does Assets automatically recognize every image of my character?
>
> Character associations come from recorded generation use and manual links. A similar-looking image is not automatically assigned to the character. Use similarity search to find media, then link an asset when appropriate.

> [accordion] Why can't I add an asset to a smart collection?
>
> Smart collections select media through rules. Change the rules or use a manual collection when you need a fixed selection. Smart collections cannot be nested in folders.

> [accordion] Why is an output still associated with an entity after I remove a manual link?
>
> Recorded generation usage is separate from a manual link. If the output was made with that entity, removing the manual link does not erase its history.

> [accordion] Why can't I use an entity on a text-only model?
>
> A text-only model cannot receive reference images. It can use an entity's description. If the entity has no description, the model form blocks that unsupported use. Add a description or choose a model that accepts image references.

> [accordion] Why is collection sharing unavailable?
>
> Creating shares requires an eligible independent self-service personal account and a manual collection. It is not available for team, organization, or enterprise source accounts, smart collections, or entities. See [Sharing collections](sharing.md).

> [accordion] Does deleting an asset delete its file?
>
> The library delete action removes the asset from browsing and search. It does not itself delete its CDN file. See [Retention and deletion](retention.md) for the separate file and request-data controls.

> [accordion] Can I restore deleted library items?
>
> Assets does not offer a Trash or restore workflow. Keep local copies of media you need and review selections before deleting.

> [accordion] Can I do everything through the public API?
>
> The API supports browsing and search, upload registration, collections, characters, tags, favorites, and ancestry. It does not expose every browser operation or CRUD endpoints for every smart entity type. See [Using the Assets API](api.md).

## Get help

If an issue continues, contact [support@fal.ai](mailto:support@fal.ai). Include the selected account, the request or asset ID if available, what you expected, and the displayed error. Do not include your API key.
