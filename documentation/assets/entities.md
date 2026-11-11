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

# Characters and smart entities

> Define reusable characters, props, environments, styles, and scenes with reference images and an @handle.

A smart entity is a named visual reference you can reuse in generations. Give it reference images and an `@handle`, then select it in a supported prompt instead of attaching and describing the same references each time.

## Choose a type

| Type | Use it for | Example |
| :- | :- | :- |
| Character | A person or creature's visual identity. | `@mara`, a recurring protagonist. |
| Prop | A recurring object or product. | `@green-bottle`, a campaign product. |
| Environment | A place, landscape, or setting. | `@workshop`, a recurring interior. |
| Style | A visual treatment, palette, medium, or texture. | `@watercolor`, a shared illustration look. |
| Scene | Staging, composition, and subject arrangement. | `@breakfast`, a recurring kitchen-table setup. |

All five types use **1–20 image references**. Choose images that make the intended reference clear. Use several angles for a prop, recognizable views of an environment, or examples of the same visual style.

A style entity uses reference images too. It is not a model fine-tune or a text-only preset. A scene is a visual reference for staging. Creating one does not assemble a 3D scene or a video timeline.

## What an entity contains

* **References:** the images that define it, plus a selected cover.
* **Name and handle:** how you find it in the library and mention it in prompts.
* **Description:** text that explains its visual characteristics. Characters require one. The other types allow it to be omitted.
* **Associated media:** generations that used the entity and assets linked to it by hand.

The defining reference images are displayed separately from the associated-media gallery. Adding a media item to that gallery does not automatically make it a reference for future generations.

## Reuse

Entity references help guide a model toward a consistent subject or look. The result depends on the model and its supported inputs. Using an entity is not a guarantee of an identical appearance in every output.

> [card] [Creating and managing entities](entities/managing.md)
>
> Add references, choose handles, and maintain associated media

> [card] [Using entities in generations](entities/using.md)
>
> Mention references in Agent and supported model prompts
