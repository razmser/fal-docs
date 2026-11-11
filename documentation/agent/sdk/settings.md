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

# Models and settings

> Choose models, change generation defaults, and update account preferences.

Use the [quickstart client](quickstart.md#create-a-client). Replace uppercase IDs with your resource IDs.

## Choose a model

The reasoning model handles the conversation. Generation models produce images, video, audio, and 3D media.

`models.listAgentModels()` returns reasoning model IDs, labels, and optional tags.
Set `general.defaultModel` to an available ID. Use `null` to restore the service default.

```ts theme={null}
import { agent } from "./client.ts";

const models = await agent.models.listAgentModels();
console.log(models);
```

After the user selects a model, save its ID:

```ts theme={null}
import { agent } from "./client.ts";

await agent.preferences.update("general", {
  defaultModel: "AGENT_MODEL_ID",
});
```

Choose a model for one response with `model`. This leaves the saved default unchanged:

```ts theme={null}
import { agent } from "./client.ts";

const response = await agent.run({
  model: "AGENT_MODEL_ID",
  input:
    "Suggest three visual directions for a running shoe campaign.",
});
console.log(response.output_text);
```

Use `fal.generation_settings_overrides` for generation choices on one response:

```ts theme={null}
import { agent } from "./client.ts";

const response = await agent.run({
  input:
    "Create a landscape product photograph of a ceramic mug.",
  fal: {
    generation_settings_overrides: {
      preferences: { aspect_ratio: "16:9" },
      reviewBeforeGenerating: true,
    },
  },
});
console.log(response.pending_inputs);
```

Generation settings require access to that feature. Unavailable controls return `403`.
Use `fal.generation_settings` when submitting a complete settings snapshot from `settings.conversations.retrieve(conversationId)`.

Search generation models with `models.list({ keywords, categories, page, limit })`.
The page starts at `1`. The default limit is `40`, with a maximum of `100`.
The result contains `items` and optional `total`, `page`, `size`, and `pages` fields.

```ts theme={null}
import { agent } from "./client.ts";

const page = await agent.models.list({
  keywords: "image",
  limit: 10,
});
console.log(page.items);

const capabilities =
  await agent.models.capabilities(
    "MODEL_ENDPOINT_ID",
  );
console.log(capabilities.fields);
```

Each model has an `id`, `modelId`, `title`, `category`, `shortDescription`, `thumbnailUrl`, and `isFavorited` flag.
An optional `modelLabId` identifies its provider.

Capabilities describe the endpoint's fields. Each field has a `name`, `label`, `type`, `description`, and `required` flag.
A field can include a `defaultValue` and additional schema properties.
Use these values to build controls. Do not assume that every model accepts the same options.

## Set generation defaults

Defaults apply at three scopes. A conversation inherits project defaults, which inherit personal defaults.
Use the returned `sources` to identify the effective source of each field.

| Target | Scope |
| - | - |
| `{ scope: "personal" }` | Account defaults. |
| `{ scope: "project", projectId }` | Defaults for a project. |
| `{ scope: "chat", chatId }` | Defaults for a conversation. `chatId` is its conversation ID. |

A defaults read returns these fields:

| Field | Meaning |
| - | - |
| `local` | Values set at the requested scope. |
| `inherited` | Settings from the parent scopes. |
| `effective` | Settings after the local values apply. |
| `sources` | Sources for effective preference and model values. |
| `inheritedSources` | Sources before local values apply. |
| `revision` | Current settings revision. |
| `projectId` | Associated project ID, or `null`. |

Use `expectedLocal` to detect a concurrent edit. Send the `local` object from your last read.

```ts theme={null}
import { agent } from "./client.ts";

const target = {
  scope: "chat",
  chatId: "CONVERSATION_ID",
} as const;
const defaults =
  await agent.settings.defaults.retrieve(target);

await agent.settings.defaults.update(target, {
  expectedLocal: defaults.local,
  changes: {
    preferences: { aspect_ratio: "16:9" },
    preferredModels: {},
  },
});
```

`changes` sets the supplied fields. Omitted fields retain their local values.
A `null` value selects automatic behavior at this scope. It does not restore inheritance.
Use `inherit` to remove a local override:

```ts theme={null}
import { agent } from "./client.ts";

const target = {
  scope: "chat",
  chatId: "CONVERSATION_ID",
} as const;
const defaults =
  await agent.settings.defaults.retrieve(target);

await agent.settings.defaults.update(target, {
  expectedLocal: defaults.local,
  inherit: { preferences: ["aspect_ratio"] },
});
```

For model defaults, use `changes.preferredModels` or `inherit.preferredModels` with a generation task name.
A selected endpoint must support that task.

| Preference | Accepted values |
| - | - |
| `aspect_ratio` | `1:1`, `16:9`, `9:16`, `4:3`, `3:4`, `3:2`, `2:3`, `21:9`. |
| `resolution` | `720p`, `1080p`, `2K`, `4K`. |
| `duration` | Positive duration in seconds, at most `3600`. |
| `generate_audio` | Boolean. |

Generation tasks are `text-to-image`, `text-to-video`, `image-to-video`, `music`, `text-to-speech`, `sound-effects`, and `image-to-3d`.
A model's capabilities can restrict which preference values it supports.
Source values are `personal`, `project`, `chat`, `generation`, and `auto`.

## Change generation settings

Use `settings.conversations` for a conversation and `settings.projects` for a project.
Both expose `retrieve(id)` and `update(id, { settings, expectedRevision })`.
An update sends the complete settings object. Preserve fields that you do not intend to change.

```ts theme={null}
import { agent } from "./client.ts";

const conversation = "CONVERSATION_ID";
const settings =
  await agent.settings.conversations.retrieve(
    conversation,
  );

await agent.settings.conversations.update(
  conversation,
  {
    settings: {
      ...settings,
      reviewBeforeGenerating: true,
    },
    expectedRevision: settings.revision,
  },
);
```

| Field | Meaning |
| - | - |
| `version` | Schema version. Use `1`. |
| `revision` | Nonnegative settings revision returned by a read. |
| `groups` | Settings keyed by generation task. |
| `groups[task].model` | Optional generation endpoint. |
| `groups[task].fields` | Model fields keyed by field name. |
| `fields[name].value` | String, finite number, boolean, string array, or `{ width, height }`. |
| `fields[name].sourceEndpointId` | Endpoint whose capabilities define the field. |
| `fields[name].label` | Display label for the field. |
| `preferences` | Shared generation preferences. |
| `preferredModels` | Effective model choices keyed by task. |
| `preferredModelOverrides` | Local model overrides, including `null` for automatic selection. |
| `defaults` | Local defaults used for inheritance. |
| `reviewBeforeGenerating` | Whether generation requires review. |

Use `settings.defaults.update` for inherited preferences and model defaults.
Use the complete settings update for `groups` and `reviewBeforeGenerating`.

On `409`, read the settings again and apply your changes to that version before submitting another update.

## Update account preferences

`preferences.retrieve()` returns the account preferences.
`preferences.update(section, input)` changes supplied fields within one section and returns the updated preferences.
Arrays replace the corresponding array. An omitted field stays unchanged.

```ts theme={null}
import { agent } from "./client.ts";

await agent.preferences.update("notifications", {
  turnComplete: false,
});
```

### General

| Field | Meaning |
| - | - |
| `preferredName` | Name used by the agent. Maximum 100 characters. An empty string uses the account name. |
| `profession` | Profile category, or `null`. See the complete enum in [AgentPreferences](types.md#agentpreferences). |
| `defaultModel` | Available reasoning model ID, or `null` for the default. |
| `liveVoice` | Voice preference for supported live interfaces. This field does not create an SDK voice session. |
| `sequencerEnabled` | Enable the video sequencer experience. |

The [type reference](types.md#agentpreferences) lists every voice and profession value.
A model or setting unavailable to the account returns `403`.

### Cost

| Field | Default | Meaning |
| - | - | - |
| `confirmImage` | `false` | Include image costs in the approval threshold. |
| `confirmVideo` | `true` | Include video costs in the approval threshold. |
| `confirmAudio` | `true` | Include audio costs in the approval threshold. |
| `confirm3d` | `true` | Include 3D costs in the approval threshold. |
| `alwaysConfirmAudio` | `false` | Require audio approval regardless of cost. |
| `alwaysConfirm3d` | `false` | Require 3D approval regardless of cost. |
| `safetyCapUsd` | `5` | Nonnegative threshold for cumulative confirmed media costs within a turn. |

The threshold requests approval. It is not a hard spending limit.

### Skills

| Field | Meaning |
| - | - |
| `enabled` | Enable skills. Defaults to `true`. |
| `disabledPresetNames` | Names of disabled preset skills. |
| `disabledFalSkillIds` | IDs of disabled fal skills. |
| `disabledSkillIds` | IDs of disabled user-installed skills. |

The disabled lists default to empty arrays.
Use [skills methods](skills.md) to discover, install, edit, or enable individual skills.

### Notifications

`turnComplete` controls completion notifications in the fal Agent interface. It defaults to `true`.
This preference does not deliver a webhook or send notifications from your application.

## Recover an uncertain update

Settings and preference writes are not retried automatically.
After a network error, read the affected resource before repeating a write.
See the [method reference](methods.md) for complete signatures and return types.
