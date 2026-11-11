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

# Adding assets

> Bring generated media and uploaded references into your fal Assets library.

The Assets library displays **images, videos, audio, and 3D assets**. Its two sources are uploaded media and generated outputs.

## Generated media

Outputs from requests included in your account's Assets configuration appear under **Generated media** and **All media**. These can come from supported fal surfaces, including model generation tools and Agent.

New media can appear in browsing before it is available in semantic search. If an output is missing, check that the generation completed. Select the correct account. Check that the configuration includes the request source. See [Access and availability](../access.md).

## Upload files

Choose **Upload media** or drop supported media files into the library. You can select several files at once. Uploads appear under **Uploads** and **All media**.

When uploading into a manual collection, the uploaded media is added to that collection. A smart collection selects assets through its rules, so uploading while viewing one does not manually add media to its results.

If an upload looks like an existing asset, the library can ask whether to continue. Review the suggested match before adding another copy.

If some files fail, check the error and retry those files. Successful uploads remain in your library.

## Upload a folder

Choose **Upload folder** to import supported media from a local folder. Assets creates a manual collection named after the folder and adds the uploaded files to it. The import groups the media into that collection. It does not recreate the folder's nested directory structure.

## Upload descriptions

Open an uploaded image or video to review its description. You can edit the description or generate a caption from the media. Use a description that helps you identify the reference later, such as the subject, setting, and visual details.

## Agent attachments

Supported media attached in Agent can be registered as library uploads and reused in later work. Generated outputs are linked to their chat. Agent also accepts documents, but the Assets browser's media categories are images, video, audio, and 3D. The Assets browser does not display a document library.

See [Assets in Agent](../../agent/assets-and-characters.md) for chat-specific behavior.

## Add media through the API

Upload external media to fal storage first, then register its fal-hosted URL in Assets. The registration endpoint accepts a URL and metadata. It is not a multipart file-upload endpoint. See [Using the Assets API](../api.md).
