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

# Quickstart

> Upload a reference, organize it into a collection, find it again, and reuse it in a generation.

This guide uses a product photo to build a small campaign library. You need a fal account with Assets enabled and permission to upload and organize media. See [Access and availability](access.md).

**1. Open your library**

Go to [fal.ai/assets](https://fal.ai/assets) and select the account you want to work in. If prompted, review the enablement terms and choose **Enable fal Assets**.

**2. Upload a reference**

Choose **Upload media** or drop your product photo into the library. Wait for the upload to finish. The image appears in **Uploads** and **All media**.

You can also start with an existing generation already in the library.

**3. Create a collection**

Create a collection called **Summer campaign**. Select the photo and add it to the collection. Collections group existing assets, so the photo stays available in the rest of your library.

**4. Find it again**

Open **All media** and enter a description such as `green bottle on a white background`. Use the **Uploads** section or media-type filter to narrow the results.

Search is based on meaning and similarity. If a new upload has not appeared in search yet, open it from **Uploads** and try search again later.

**5. Reuse it**

Open a model that accepts an image input. Open the input's asset picker and select your photo. Or, if you have Agent access, type `@` in a chat and select the photo from **Assets**.

For example, ask Agent: `Use this bottle as the product reference for a summer campaign hero image on pale sand.` Model generations require the appropriate access and credits.

**6. Download the result**

Open the completed asset and choose **Download**. For multiple files, select them in the library and use the selection toolbar's **Download** action.

## Next steps

* Create a reusable product reference with [Characters and smart entities](entities.md).
* Group future matching media with a [smart collection](organizing/smart-collections.md).
* Learn how to [share a collection](sharing.md).
