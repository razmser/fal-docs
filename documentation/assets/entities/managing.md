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

# Creating and managing entities

> Create reusable references, manage their images and handles, and organize associated generations.

**1. Choose the entity type**

Create a new entity in Assets and choose **Character**, **Prop**, **Environment**, **Style**, or **Scene**. See [Characters and smart entities](../entities.md) for examples.

**2. Add reference images**

Upload images or select existing images from your library. An entity needs at least one reference and supports up to twenty. Choose a reference image as its cover.

If you need new references, use **Generate with Agent** to describe the shots you want. This requires Agent access and starts a generation workflow. The entity is not created merely by opening it.

**3. Name and describe it**

Enter a name and review the suggested handle. Handles must be available in the selected account, across all five entity types.

Add a concise description, or use the description-generation control to describe the reference images. Review the result before saving. A character requires a description.

**4. Create**

Choose the create action for your entity type. Open its page to review its references and associated media.

## Edit references

Open the entity's edit action to change its name, description, reference images, or cover.
For props, environments, styles, and scenes, the cover must be one of the references.
Characters can also have a separate cover image.
Reference edits affect future use. They do not regenerate existing outputs.

The type is chosen at creation and cannot be changed in the edit form. Character handles also cannot be changed after creation. Props, environments, styles, and scenes allow handle edits, subject to availability.

## Associated media

An entity's gallery includes recorded generations that used it and media you link manually. You can link an existing asset from the asset's entity controls.

These associations have different meanings:

| Action | Effect |
| :- | :- |
| Add or remove a reference in the entity editor | Changes the images used to define the entity. |
| Link an asset to the entity | Adds a manual association for organization. |
| Remove a manual link | Removes that association. It does not erase recorded generation usage. |
| Generate with the entity | Records usage when the supported generation workflow captures it. |

## Delete an entity

Deleting an entity removes the reusable entity. It does not delete its reference files or generated media from the library.

Deleting a reference asset from the library makes that reference unavailable to the entity. Keep the entity's reference set available if you intend to reuse it. See [Retention and deletion](../retention.md).
