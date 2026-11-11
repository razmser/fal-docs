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

# Using the Assets API

> Browse, search, register uploads, and organize assets with the fal Platform API.

The Assets endpoints are part of the [fal Platform API](../../api-reference/platform-apis/for-assets.md), at `https://api.fal.ai/v1/assets`.

## Before you start

Enable Assets for the account you want to use and create an [API key](../setting-up/authentication.md). Set `FAL_KEY` in your environment and send it as `Authorization: Key ...`. Keep the key on your server, not in browser code.

Library access and which generation sources enter the library are separate concerns. If API generations are missing, check the account configuration. See [Access and availability](access.md).

## Browse or search

```bash theme={null}
curl --get 'https://api.fal.ai/v1/assets' \
  -H "Authorization: Key $FAL_KEY" \
  --data-urlencode 'q=green bottle on sand' \
  --data-urlencode 'media_type=image' \
  --data-urlencode 'limit=20'
```

Omit `q` for ordinary browsing. For example search, send `search_image_url` or `search_video_url` with a fal-hosted media URL. Use one search input at a time.

You can also filter by `section`, `source`, `collection_id`, `tag_id`, `tag_mode`, and `character_identifier`. Character-handle filtering is based on associations with that character. It is not automatic visual recognition.

The response contains `assets`, `next_cursor`, `has_more`, `total_count`, and `scope_truncated`. To continue, send `next_cursor` as `cursor` with the same query and filters. Treat cursors as opaque. A null total means a total is unavailable. If `scope_truncated` is true, narrow the scope rather than treating the response as a complete set.

See [Browse assets](../../platform-apis/v1/assets.md) for parameters and response fields.

## Register an upload

First upload your file to [fal storage](../../api-reference/client-libraries/javascript/storage.md). Then register the returned URL with Assets. Replace the example URL below with your own fal-hosted URL:

```bash theme={null}
curl -X POST 'https://api.fal.ai/v1/assets/uploads' \
  -H "Authorization: Key $FAL_KEY" \
  -H 'Content-Type: application/json' \
  -d '{
    "url": "https://fal.media/files/your-upload.png",
    "type": "image",
    "prompt": "Green product bottle photographed from the front",
    "favorite": true
  }'
```

This endpoint registers a file already hosted on fal. It does not accept multipart file data or arbitrary external URLs. Supported types are `image`, `video`, `audio`, and `3d`. You can provide a manual `collection_id` and `tag_ids` during registration. See [Upload asset](../../platform-apis/v1/assets/uploads.md).

## Organize an asset

Create a manual collection:

```bash theme={null}
curl -X POST 'https://api.fal.ai/v1/assets/collections' \
  -H "Authorization: Key $FAL_KEY" \
  -H 'Content-Type: application/json' \
  -d '{"name": "Summer campaign", "description": "Product campaign references"}'
```

Use the returned collection ID and a `vector_id` returned by browse or upload. Set `COLLECTION_ID` and `VECTOR_ID` to those values:

```bash theme={null}
curl -X POST "https://api.fal.ai/v1/assets/collections/$COLLECTION_ID/assets" \
  -H "Authorization: Key $FAL_KEY" \
  -H 'Content-Type: application/json' \
  -d "{\"vector_id\": \"$VECTOR_ID\"}"
```

The collection API supports listing, editing, moving, reading hierarchy, adding and removing members, and deletion. Smart collections use a `filters` object at creation and follow the [smart-collection rules](organizing/smart-collections.md).

Use the [tag endpoints](../../platform-apis/v1/assets/tags.md) and [favorite endpoint](../../platform-apis/v1/assets/favorite.md) for labels and favorite state. Setting an asset's full tag set replaces that set. Assigning one tag adds only that association.

## Characters

The public [character endpoints](../../platform-apis/v1/assets/characters.md) support creating, reading, updating, favoriting, and deleting characters. A character needs a description and one to twenty reference images. See [Create asset character](../../platform-apis/v1/assets/characters/create.md) for the reference format.

The browser supports all five smart entity types. The public Assets API currently exposes character CRUD, not equivalent CRUD endpoints for props, environments, styles, and scenes.

The [Agent SDK library client](../agent/sdk/library.md) supports all five types through the Agent SDK endpoint.
Its library methods do not start agent execution.

## Generation ancestry

Set `ASSET_ID` to the catalog `asset_id` returned by Assets to retrieve recorded ancestry. This is separate from the `vector_id` used in the collection example:

```bash theme={null}
curl --get "https://api.fal.ai/v1/assets/$ASSET_ID/lineage" \
  -H "Authorization: Key $FAL_KEY" \
  --data-urlencode 'depth=3'
```

The response describes recorded inputs, generation requests, and referenced entities. Deleted or expired ancestors can remain as unavailable references in the history. Their presence does not make their media downloadable.

## API boundaries

The public Assets endpoints do not expose collection-share management or the browser's asset-delete operation. To delete a completed generation's request payloads and CDN output files, use [Delete request payloads](../../platform-apis/v1/models/requests/payloads.md). See [Retention and deletion](retention.md) for the difference.
