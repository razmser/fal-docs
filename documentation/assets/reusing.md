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

# Reusing assets across fal

> Use existing library media as model inputs, Agent attachments, and project references.

An asset can be used again without downloading it and uploading it for every generation. Select it from your library wherever a fal surface offers an asset picker.

## Model inputs

In a supported Playground, Workflow, or Sandbox media input, open the asset picker. Browse or search the library, select an item of the appropriate type, and review the input before running the model.

The model's input schema determines what it accepts. An image-to-video model needs an image. A text-only model cannot consume an image reference merely because it exists in Assets.

For a reusable named set of visual references, use [Characters and smart entities](entities.md).

## Agent chats

With Agent access, type `@` and open **Assets** to select library media. You can also attach a generation from the current chat or drag a tile from the media rail into the composer.

Give the asset a clear role in your request:

```text theme={null}
Use the selected image as the first frame. Animate a slow camera push-in
and keep the product label readable.
```

Agent can search your library on your behalf. Ask for the media you remember and explain how to use it. See [Assets in Agent](../agent/assets-and-characters.md).

## Agent projects

Add collections and reusable references to a project when several chats need the same source material. The project makes those resources available as context across its chats. See [Projects](../agent/projects.md).

## Outside fal

[Download assets](downloading.md) for local editing, or use the [Assets API](api.md) to retrieve available media URLs for your application. Access rules and file lifetime still apply when you reuse a URL.
