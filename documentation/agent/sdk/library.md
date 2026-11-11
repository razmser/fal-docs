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

# Library assets, collections, and entities

> Manage assets, manual and smart collections, and all five smart entity types.

Use the [quickstart client](quickstart.md#create-a-client). Replace uppercase IDs with your resource IDs.

Manage assets, collections, and reusable visual references in your fal Assets library.

The API key selects the personal or team account.
The library enforces the same account access, ownership, and read/write permissions as Assets.
See [Access and availability](../../assets/access.md) for the product rules.

## Use the correct asset ID

Use a non-null `assetRecordId` for library mutations. A search result can have a missing or null `assetRecordId`.

| Field | Use |
| - | - |
| `assetRecordId` | Retrieve, favorite, update, delete, or add the library record to a collection. |
| `assetId` | Attach an existing asset to a project. Can be `null`. |
| `vectorId` | Search identity. Do not use it for library mutations. |
| `requestId` | Generation request identity. Can be `null`. |

An SDK artifact has its own ID. It is not a library record.

## Search assets

```ts theme={null}
import { agent } from "./client.ts";

const page = await agent.library.assets.list({
  q: "blue ceramic mug",
  mediaTypes: ["image"],
  limit: 20,
});

console.log(
  page.items,
  page.nextCursor,
  page.scopeTruncated,
);
```

All query fields are optional:

| Field | Values and behavior |
| - | - |
| `q` | Text search. Defaults to an empty string. |
| `searchImageUrl` | Image URL for similarity search. |
| `searchVideoUrl` | Video URL for similarity search. |
| `mediaTypes` | Array containing `image`, `video`, `audio`, or `3d`. |
| `sources` | Array containing `upload` or `response`. |
| `section` | `all-media`, `generated`, `uploads`, or `favorites`. Defaults to `all-media`. |
| `endpoints` | Generation endpoint IDs. |
| `collectionId` | Restrict the search to a collection. |
| `recursive` | Include descendant collections when searching a collection. |
| `assetRecordIds` | Restrict results to these records. Maximum 10,000 IDs. |
| `tagIds` | Tag IDs used to filter results. |
| `tagMode` | `any` or `all`. Defaults to `any`. |
| `sortOrder` | `newest` or `oldest`. Defaults to `newest`. |
| `cursor` | The previous page's `nextCursor`. Omit it or use `null` for the first page. |
| `limit` | From 1 to 100. Defaults to 48. |

The response contains `items`, `nextCursor`, `totalCount`, and `scopeTruncated`.
A null `totalCount` means the total is unavailable.
A true `scopeTruncated` means the search does not cover the full scope.

```ts theme={null}
import { agent } from "./client.ts";

let cursor: string | null = null;
do {
  const page = await agent.library.assets.list({
    section: "favorites",
    mediaTypes: ["image"],
    cursor,
  });
  console.log(page.items);
  if (page.scopeTruncated)
    console.warn("The search scope is incomplete.");
  cursor = page.nextCursor;
} while (cursor);
```

Keep the filters unchanged when advancing the cursor.
Start a new search when you change the filters.

## Read asset metadata

`library.assets.retrieve(assetRecordId)` returns an [AgentLibraryAsset](types.md#agentlibraryasset).
Search results use the same type.

| Fields | Meaning |
| - | - |
| `url`, `type`, `title` | Media location, modality, and display title. The URL can be `null`. |
| `endpoint`, `source`, `prompt` | Generation endpoint, origin, and prompt. Each can be `null`. |
| `width`, `height`, `size`, `contentType` | Media dimensions, byte size, and MIME type when available. |
| `createdAt` | Creation time, or `null`. |
| `isFavorited`, `collectionIds` | Favorite state and collection memberships. |
| `tags` | Optional tag records with ID, name, color, and creation time. |

## Register and update assets

Register an existing media URL:

```ts theme={null}
import { agent } from "./client.ts";

const asset = await agent.library.assets.register({
  url: "https://fal.media/files/FILE_ID/mug.png",
  type: "image",
  favorite: true,
});

console.log(asset.assetRecordId);
```

Replace the example URL with the HTTPS URL returned by the fal storage client.
Arbitrary external hosts are rejected. Upload media with `fal.storage.upload` before registration.
Registration does not upload the file.
Optional inputs include `size` in bytes, `collectionId`, and `favorite`.

| Method | Behavior |
| - | - |
| `library.assets.setFavorite(id, boolean)` | Set the favorite state. Returns the record identity and `isFavorited`. |
| `library.assets.updatePrompt(id, prompt)` | Update an uploaded asset's prompt. Use 1–2,000 characters after trimming. |
| `library.assets.delete(id)` | Delete the library record. Returns `{ success: true }`. |

The prompt update applies to uploaded assets. It does not rewrite a generated asset's original prompt.

```ts theme={null}
import { agent } from "./client.ts";

await agent.library.assets.setFavorite(
  "ASSET_RECORD_ID",
  true,
);
await agent.library.assets.updatePrompt(
  "ASSET_RECORD_ID",
  "Blue ceramic mug",
);
```

## Manage tags

```ts theme={null}
import { agent } from "./client.ts";

const tag = await agent.library.tags.create({
  name: "launch",
});
await agent.library.assets.assignTag(
  "ASSET_RECORD_ID",
  tag.id,
);
console.log(
  await agent.library.assets.tags(
    "ASSET_RECORD_ID",
  ),
);
```

`library.tags.list` returns available asset tags. System tags are excluded.
Use `library.tags.update(id, change)` to change a name or color.
Tag names are trimmed, stored in lowercase, and unique within the account.
Use `library.assets.removeTag(assetRecordId, tagId)` to remove one assignment.
Deleting a tag removes its assignments without deleting assets.

## Find smart entities

`library.entities` supports all five types through the same methods.

| Type | Reference purpose |
| - | - |
| `character` | A person or creature. |
| `prop` | An object or product. |
| `environment` | A location or setting. |
| `style` | A visual treatment or medium. |
| `scene` | A composition or arrangement. |

List the account's entities, or filter by type:

```ts theme={null}
import { agent } from "./client.ts";

const entities = await agent.library.entities.list({
  types: [
    "character",
    "prop",
    "environment",
    "style",
    "scene",
  ],
  limit: 100,
  offset: 0,
});

for (const entity of entities) {
  console.log(
    entity.id,
    entity.type,
    entity.name,
    entity.handle,
  );
}
```

Omit `types` to include all five types.
The default limit is 100. The maximum is 1,000.
Use an offset to read another page. The response is an array without a pagination cursor.

Use `search` for a case-insensitive substring match against entity names and handles.
The search text has a maximum of 255 characters after trimming.

```ts theme={null}
import { agent } from "./client.ts";

const props = await agent.library.entities.list({
  types: ["prop"],
  search: "mug",
});
console.log(props);
```

Resolve known handles without searching every page:

```ts theme={null}
import { agent } from "./client.ts";

const entities =
  await agent.library.entities.resolve({
    handles: [
      "@milo",
      "@blue-mug",
      "@workshop",
      "@watercolor",
      "@breakfast",
    ],
  });

console.log(entities);
```

`resolve` accepts an optional `types` filter.
Supply one to 100 handles, with or without the leading `@`.
It returns matching entities in the key's account. Unknown handles and handles from other accounts are omitted.

## Read entity metadata and references

```ts theme={null}
import { agent } from "./client.ts";

const entity =
  await agent.library.entities.retrieve(
    "ENTITY_ID",
  );
console.log(
  entity.type,
  entity.description,
  entity.metadata,
  entity.thumbnailUrl,
);

const referenceUrls = entity.references.map(
  (reference) => reference.url,
);
console.log(referenceUrls);
```

List, resolve, retrieve, create, and update results contain the same typed entity data.
Each defining reference includes an `assetRecordId` and a usable image `url`.
The `references` array excludes the associated-media gallery.
Deleted or expired reference assets are unavailable.

`metadata` contains product-specific fields. Check their shape before use.
The character metadata is managed by the product and cannot be set through entity writes.

## Create and update smart entities

This example creates one entity of each type.
Replace each reference URL with a fal storage URL or an existing asset target before execution.

```ts theme={null}
import { agent } from "./client.ts";

const inputs = [
  {
    type: "character",
    name: "Milo",
    handle: "milo",
    description: "A gray cat with green eyes.",
    reference: "MILO",
  },
  {
    type: "prop",
    name: "Blue mug",
    handle: "blue-mug",
    description: "A blue ceramic mug.",
    reference: "MUG",
  },
  {
    type: "environment",
    name: "Workshop",
    handle: "workshop",
    description: "A sunlit pottery workshop.",
    reference: "WORKSHOP",
  },
  {
    type: "style",
    name: "Watercolor",
    handle: "watercolor",
    description:
      "Soft watercolor with visible paper texture.",
    reference: "WATERCOLOR",
  },
  {
    type: "scene",
    name: "Breakfast",
    handle: "breakfast",
    description:
      "A mug and plate on a kitchen table.",
    reference: "BREAKFAST",
  },
] as const;

for (const { reference, ...input } of inputs) {
  const entity =
    await agent.library.entities.create({
      ...input,
      referenceImages: [
        `https://fal.media/files/${reference}.png`,
      ],
    });
  console.log(entity.id, entity.type);
}
```

Names contain 1–255 characters after trimming. Descriptions have a maximum of 2,000 characters.
Characters require a description. The other four types allow an omitted or null description.
All five types require one to twenty reference images.
The product validates storage URLs, asset access, and image types.

Handles are unique across the account's five entity types.
Handles have a maximum of 64 characters.
The product normalizes handles and derives a handle from the name when omitted during creation.
Use `entities.checkHandle({ handle, excludeId })` to check availability.
Supply `excludeId` when editing an existing entity.
Another request can claim the handle before the write completes.

```ts theme={null}
import { agent } from "./client.ts";

const availability =
  await agent.library.entities.checkHandle({
    handle: "blue-mug",
    excludeId: "PROP_ID",
  });
console.log(
  availability.handle,
  availability.available,
);

const prop = await agent.library.entities.update(
  "PROP_ID",
  {
    description:
      "A blue ceramic mug with a rounded handle.",
    referenceImages: [
      "https://fal.media/files/MUG_FRONT.png",
      "https://fal.media/files/MUG_SIDE.png",
    ],
    coverImageUrl:
      "https://fal.media/files/MUG_FRONT.png",
  },
);
console.log(prop.references);
```

Updates preserve omitted fields. A supplied `referenceImages` array replaces the complete defining reference set.
The entity type cannot change. Character handles cannot change.
The other four types allow handle edits and nullable metadata.
Set their handle to `null` to clear it. Retrieve them by ID or search by name afterward.
Handle resolution does not return entities with a cleared handle.
Their cover must be one of the defining references.
Characters also support a separate cover image, which does not become a defining reference.

## Manage the associated-media gallery

The gallery contains manually linked assets and recorded generations that used the entity.
Adding an asset to the gallery does not add a defining reference.

```ts theme={null}
import { agent } from "./client.ts";

await agent.library.entities.addAsset(
  "ENTITY_ID",
  "ASSET_RECORD_ID",
);

const gallery =
  await agent.library.entities.listAssets(
    "ENTITY_ID",
    {
      mediaTypes: ["image"],
      limit: 50,
      offset: 0,
    },
  );
console.log(
  gallery.items,
  gallery.nextOffset,
  gallery.totalCount,
);

await agent.library.entities.removeAsset(
  "ENTITY_ID",
  "ASSET_RECORD_ID",
);
```

By default, a reference appears in the gallery only if it also has a manual link or recorded generation usage.
Set `includeReferences: true` to include the defining reference membership too.
The default limit is 50. The maximum is 100.
Use the returned `nextOffset` for the next page. A null value ends pagination.
Removing a manual link does not delete the asset or erase recorded generation usage.

Use `entities.setFavorite(id, boolean)` and `entities.delete(id)` to favorite or delete any entity type.
Deleting an entity does not delete its source assets.
The existing collection favorite, delete, add-asset, and remove-asset methods also accept entity IDs.

## Character methods

The `library.characters` methods keep their existing inputs and results:

```ts theme={null}
import { agent } from "./client.ts";

const character =
  await agent.library.characters.create({
    name: "Milo",
    identifier: "milo",
    description: "A gray cat with green eyes.",
    referenceImages: [
      "https://fal.media/files/REFERENCE.png",
    ],
  });
console.log(character.id);
```

Use `characters.checkIdentifier(identifier)` to check availability before creation.
Use `characters.update(id, input)` to replace the name, description, and complete reference list.
Character identifiers cannot change. `characters.references(id)` reads the resolved reference images.
The legacy references response can include a display-only cover marked `isCover`.
Exclude that entry when saving `referenceImages`.

Use `library.entities.list({ types: ["character"] })` to list only characters.
The asset query's `characterSearchIdentifiers` field filters assets by character usage.
See the [reference](methods.md#library) for exact inputs and results.

## Create a manual collection

```ts theme={null}
import { agent } from "./client.ts";

const collection =
  await agent.library.collections.create({
    name: "Mugs",
  });
await agent.library.collections.addAsset(
  collection.id,
  "ASSET_RECORD_ID",
);
```

A collection name contains 1–255 characters.
Optional fields include `description`, `icon`, `color`, `coverImageUrl`, `filters`, and `parentCollectionId`.
The description has a maximum of 1,000 characters. The icon and color each have a maximum of 32 characters.

Use `removeAsset(collectionId, assetRecordId)` to remove a manual membership.
Adding or removing membership does not upload or delete the asset.

## Create a smart collection

Smart collections select assets through a filter expression.
This example selects images with a similar description:

```ts theme={null}
import { agent } from "./client.ts";

const collection =
  await agent.library.collections.create({
    name: "Blue product photos",
    filters: {
      and: [
        { field: "type", op: "eq", value: "image" },
        {
          semantic: {
            text: "blue product photography",
            min_similarity: 0.6,
          },
        },
      ],
    },
  });

console.log(collection.id);
```

A filter uses one of these forms:

| Form | Meaning |
| - | - |
| `{ and: [...] }` | Match every child expression. Requires at least one child. |
| `{ or: [...] }` | Match any child expression. Requires at least one child. |
| `{ field, op, value }` | Compare an asset field with a value. |
| `{ semantic: { ... } }` | Match semantic similarity. |

Comparison fields are `endpoint`, `status`, `type`, `source`, and `created_at`.
Operators are `eq`, `neq`, `in`, `gt`, `gte`, `lt`, and `lte`.
Values can be strings, numbers, string arrays, or number arrays.
Use a value compatible with the field. For `in`, supply an array.

A semantic expression requires exactly one of `text`, `image_url`, or `video_url`.
`min_similarity` ranges from `0` to `1`.
The maximum filter depth is three levels, including the root expression.

Use `library.assets.list({ collectionId })` to read matching assets.
Manage smart collection membership through its filters.

## List and manage collections

`library.collections.list({ limit, offset, includeCharacters, includeSmartEntities })` returns an array.
`includeCharacters` defaults to `true`. It preserves the existing character inclusion behavior.
Set `includeSmartEntities: true` to include all five entity types, regardless of `includeCharacters`.
When `includeSmartEntities` is false or omitted, `includeCharacters` controls character inclusion.
The limit ranges from 1 to 1,000. The offset ranges from 0 to 10,000.
This method does not return `nextCursor`.

```ts theme={null}
import { agent } from "./client.ts";

const collections =
  await agent.library.collections.list({
    limit: 50,
    offset: 0,
    includeSmartEntities: true,
  });
console.log(collections);
```

| Method | Behavior |
| - | - |
| `library.collections.update(id, input)` | Change metadata or filters. Omitted fields stay unchanged. |
| `library.collections.move(id, parentId)` | Move the collection. Use `null` for the root. |
| `library.collections.setFavorite(id, boolean)` | Set the favorite state. |
| `library.collections.delete(id)` | Delete the collection without deleting its source assets. |

Updates accept `name`, `description`, `icon`, `color`, `coverImageUrl`, and `filters`.
Use `null` to clear a nullable field. The name cannot be `null`.

An [AgentLibraryCollection](types.md#agentlibrarycollection) includes metadata, type, filters, parent ID, handle fields, favorite state, and timestamps.
It also includes `assetCount` and `previewAssets`. The count can be `null`.
Types are `manual`, `smart`, `character`, `prop`, `environment`, `style`, and `scene`.
Use `library.entities` to create or update an entity's defining data.
Use `library.collections` to create manual or smart collections.

## Handle uncertain writes

Library writes are sent once. They do not accept an idempotency key.
After a network error, retrieve the asset or list the collections before repeating the change.
All methods accept optional `signal` and `timeoutMs` as their final argument.
