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

# Generation details and history

> Inspect an asset's prompt and metadata, follow its inputs, and find generations that used it.

Open an asset to view the information available for it. Uploaded references and generated outputs have different details.

## Prompt and file details

A generated asset can show the prompt, model endpoint, and request ID that produced it. Uploaded images and videos have an editable description instead.

The details panel also shows available file information, such as dimensions, aspect ratio, format, content type, size, and creation date. Some fields can be unavailable for older media or particular file types. Your role must permit viewing request payloads to inspect protected generation details.

## Inputs

**Inputs** shows media used to produce the generation. Open an available input to inspect it and continue following its history.

For example, an image-to-video result can link back to the source image, which in turn can show the reference images used to generate it.

## Used in

**Used in** shows recorded later generations that consumed the asset. Open one to see what was made from it.

These panels appear when relationships are available. They show captured history, not a guarantee that every past use or externally created edit is recorded. Deleted or expired items can become unavailable.

## Entity associations

The asset's **Entities** section connects it to reusable characters and other smart entities. Distinguish references used during a generation from assets you link afterward to organize an entity's gallery.

Removing a manual link does not erase the fact that an entity was used to produce a generation. See [Creating and managing entities](../entities/managing.md).

## Programmatic history

The Assets API exposes an asset's ancestry through `GET /v1/assets/{asset_id}/lineage`. Use it to inspect recorded inputs and generation relationships. See [Using the Assets API](../api.md).
