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

# Type reference

> Complete field definitions for Agent requests, responses, settings, projects, and library resources.

Import the `Agent*` types from `@fal-ai/client`. A `?` marks an optional field. A `null` value is distinct from an omitted field.
`InputRequestBase` defines shared fields within `AgentInputRequest`. It is not a separate package export.

The declarations describe the SDK types. The [request limits](responses.md#request-fields) define accepted request values.
Generic JSON fields have no additional stable schema. Check their shape before reading product-specific data.

## AgentStatus

```typescript Definition theme={null}
export type AgentStatus =
  | "queued"
  | "in_progress"
  | "completed"
  | "incomplete"
  | "failed"
  | "cancelled";
```

## AgentJson

```typescript Definition theme={null}
export type AgentJson =
  | null
  | boolean
  | number
  | string
  | AgentJson[]
  | {
      [key: string]: AgentJson;
    };
```

## AgentFailure

```typescript Definition theme={null}
export interface AgentFailure {
  code: string;
  message: string;
}
```

## AgentPlanStep

```typescript Definition theme={null}
export type AgentPlanStep = {
  id: string;
  label: string;
  detail?: string;
  endpoint_id?: string;
  model_pinned?: boolean;
  requires_approval?: boolean;
};
```

## AgentBlock

```typescript Definition theme={null}
export interface AgentBlock {
  type: "fal.block";
  id: string;
  kind: string;
  revision: number;
  fallback_text: string;
  data: AgentJson;
}
```

Related: [AgentJson](types.md#agentjson).

## AgentPlanBlock

```typescript Definition theme={null}
export interface AgentPlanBlock
  extends Omit<AgentBlock, "data"> {
  kind: "plan";
  data: {
    title?: string;
    steps: AgentPlanStep[];
  };
}
```

Related: [AgentBlock](types.md#agentblock), [AgentPlanStep](types.md#agentplanstep).

## AgentText

```typescript Definition theme={null}
export interface AgentText {
  type: "output_text";
  text: string;
  annotations: AgentJson[];
}
```

Related: [AgentJson](types.md#agentjson).

## AgentMessage

```typescript Definition theme={null}
export interface AgentMessage {
  id: string;
  type: "message";
  role: "assistant";
  status:
    | "in_progress"
    | "completed"
    | "incomplete";
  content: Array<AgentText | AgentBlock>;
}
```

Related: [AgentText](types.md#agenttext), [AgentBlock](types.md#agentblock).

## AgentOperation

Generation operations use the run ID as `id`. Capability labels do not introduce additional client methods. See [runs](queue.md).

```typescript Definition theme={null}
export interface AgentOperation {
  id: string;
  type: "fal.operation";
  kind: string;
  name: string;
  status:
    | "queued"
    | "in_progress"
    | "completed"
    | "failed"
    | "cancelled";
  parent_id?: string;
  revision?: number;
  capabilities?: Array<"rename" | "move_after">;
  artifact_ids: string[];
  error: AgentFailure | null;
  progress?: {
    completed: number;
    total?: number;
    message?: string;
  };
}
```

Related: [AgentFailure](types.md#agentfailure).

## AgentArtifact

Media artifacts use revision `1`. Read optional files before using their URLs. See [media](media.md).

```typescript Definition theme={null}
export interface AgentArtifact {
  id: string;
  type: "fal.artifact";
  kind: "media" | "file" | "data" | "composition";
  media_type?: "image" | "video" | "audio" | "3d";
  revision: 1;
  produced_by?: string;
  files?: Array<{
    role: string;
    url: string;
    mime_type: string;
    url_expires_at?: string | null;
  }>;
  data?: AgentJson;
  metadata?: Record<string, AgentJson>;
}
```

Related: [AgentJson](types.md#agentjson).

## AgentQuestion

```typescript Definition theme={null}
export interface AgentQuestion {
  id: string;
  text: string;
  multiple: boolean;
  options: Array<{
    id: string;
    label: string;
    description?: string;
  }>;
  allow_text: boolean;
}
```

## InputRequestBase

```typescript Definition theme={null}
interface InputRequestBase {
  id: string;
  type: "fal.input_request";
  status:
    | "pending"
    | "answered"
    | "rejected"
    | "expired"
    | "cancelled";
  prompt: string;
  expires_at?: string;
}
```

## AgentInputRequest

```typescript Definition theme={null}
export type AgentInputRequest = InputRequestBase &
  (
    | {
        kind: "clarification";
        questions: AgentQuestion[];
      }
    | {
        kind: "approval";
        target: {
          item_id: string;
          revision: number;
        };
        accepted_answers: Array<
          "approve" | "reject"
        >;
      }
  );
```

Related: [AgentQuestion](types.md#agentquestion).

## AgentAnswer

```typescript Definition theme={null}
export type AgentAnswer =
  | {
      kind: "answers";
      answers: Array<{
        question_id: string;
        selected_option_ids: string[];
        text?: string;
      }>;
    }
  | {
      kind: "approval";
      decision: "approve" | "reject";
    };
```

## AgentOutputItem

```typescript Definition theme={null}
export type AgentOutputItem =
  | AgentMessage
  | AgentOperation
  | AgentArtifact
  | AgentInputRequest;
```

Related: [AgentMessage](types.md#agentmessage), [AgentOperation](types.md#agentoperation), [AgentArtifact](types.md#agentartifact), [AgentInputRequest](types.md#agentinputrequest).

## AgentActivity

```typescript Definition theme={null}
export type AgentActivity = {
  id: string;
  turn_id: string;
  label: string;
  status: "running" | "completed";
  started_at: number;
  ended_at?: number;
  duration_ms?: number;
} & (
  | {
      kind: "tool";
      node: string;
      name?: string;
      arguments?: AgentJson;
      output?: AgentJson;
    }
  | {
      kind: "reasoning";
      content: string;
    }
);
```

Related: [AgentJson](types.md#agentjson).

## AgentResponse

```typescript Definition theme={null}
export interface AgentResponse {
  id: string;
  status: AgentStatus;
  output: AgentOutputItem[];
  error: AgentFailure | null;
  usage: {
    input_tokens?: number;
    output_tokens?: number;
    cost?: {
      currency: "USD";
      estimated?: number | null;
      reserved?: number | null;
      settled?: number | null;
    };
  } | null;
  fal: {
    model_usage?: {
      scope: "conversation_model";
      input_tokens?: number;
      output_tokens?: number;
    } | null;
    pending_submission?: {
      input_request_id: string;
      action: "retry_input";
      error: AgentFailure;
    };
    conversation_id: string;
    phase:
      | "queued"
      | "running"
      | "waiting_for_input"
      | "cancelling"
      | "finished";
    sequence_number: number;
    pending_input_ids: string[];
    final_artifact_ids: string[];
    activities?: AgentActivity[];
    activated_skills?: string[];
  };
}
```

Related: [AgentStatus](types.md#agentstatus), [AgentOutputItem](types.md#agentoutputitem), [AgentFailure](types.md#agentfailure), [AgentActivity](types.md#agentactivity).

## AgentResponseView

```typescript Definition theme={null}
export interface AgentResponseView
  extends AgentResponse {
  readonly output_text: string;
  readonly artifacts: AgentArtifact[];
  readonly final_artifacts: AgentArtifact[];
  readonly pending_inputs: AgentInputRequest[];
}
```

Related: [AgentResponse](types.md#agentresponse), [AgentArtifact](types.md#agentartifact), [AgentInputRequest](types.md#agentinputrequest).

## AgentInputContent

Artifact references accept revision `1` or an omitted revision. File and image parts reference URLs without uploading content.

```typescript Definition theme={null}
export type AgentInputContent =
  | {
      type: "input_text";
      text: string;
    }
  | {
      type: "input_image";
      image_url: string;
    }
  | {
      type: "input_file";
      file_url: string;
      mime_type?: string;
    }
  | {
      type: "fal.input_artifact";
      artifact_id: string;
      revision?: 1;
    };
```

## AgentRequest

Use `fal.skills` to activate skills by name. See [request limits](responses.md#request-fields) and [skills](skills.md).

```typescript Definition theme={null}
export type AgentRequest = {
  model?: string;
  input:
    | string
    | [
        {
          role: "user";
          content: AgentInputContent[];
        },
      ];
  fal?: {
    generation_settings?: AgentGenerationSettings;
    generation_settings_overrides?: AgentGenerationSettingsOverrides;
    on_ambiguity?: "ask";
    skills?: string[];
  };
} & (
  | {
      conversation?: string;
      previous_response_id?: never;
    }
  | {
      conversation?: never;
      previous_response_id?: string;
    }
);
```

Related: [AgentInputContent](types.md#agentinputcontent), [AgentGenerationSettings](types.md#agentgenerationsettings), [AgentGenerationSettingsOverrides](types.md#agentgenerationsettingsoverrides).

## AgentRequestOptions

```typescript Definition theme={null}
export interface AgentRequestOptions {
  signal?: AbortSignal;
  timeoutMs?: number;
  idempotencyKey?: string;
}
```

## AgentRunOptions

```typescript Definition theme={null}
export interface AgentRunOptions
  extends AgentRequestOptions {
  pollIntervalMs?: number;
  onAccepted?: (
    response: AgentResponseView,
  ) => void;
}
```

Related: [AgentRequestOptions](types.md#agentrequestoptions), [AgentResponseView](types.md#agentresponseview).

## AgentStreamOptions

```typescript Definition theme={null}
export interface AgentStreamOptions
  extends AgentRunOptions {
  maxReconnects?: number;
  reconnectDelayMs?: number;
}
```

Related: [AgentRunOptions](types.md#agentrunoptions).

## AgentPage

```typescript Definition theme={null}
export interface AgentPage<T> {
  data: T[];
  next_cursor: string | null;
}
```

## AgentPageOptions

```typescript Definition theme={null}
export interface AgentPageOptions
  extends AgentRequestOptions {
  cursor?: string;
  limit?: number;
}
```

Related: [AgentRequestOptions](types.md#agentrequestoptions).

## AgentConversation

```typescript Definition theme={null}
export interface AgentConversation {
  id: string;
  title: string | null;
  active_response_ids: string[];
}
```

## AgentConversationItem

```typescript Definition theme={null}
export type AgentConversationItem = {
  id: string;
  response_id: string | null;
  sequence_number: number;
} & (
  | {
      type: "input";
      input: AgentRequest["input"];
    }
  | {
      type: "answer";
      input_request_id: string;
      answer: AgentAnswer;
    }
  | {
      type: "output";
      item: AgentOutputItem;
    }
);
```

Related: [AgentRequest](types.md#agentrequest), [AgentAnswer](types.md#agentanswer), [AgentOutputItem](types.md#agentoutputitem).

## AgentPlanUpdate

```typescript Definition theme={null}
export type AgentPlanUpdate = {
  conversation: string;
  expected_revision: number;
  title?: string;
  steps: Array<{
    id?: string;
    label: string;
    endpoint_id?: string | null;
    model_pinned: boolean;
    requires_approval: boolean;
  }>;
};
```

## AgentEvent

This is a transport envelope. Public stream methods yield `AgentResponseView` snapshots, not `AgentEvent` objects.

```typescript Definition theme={null}
export interface AgentEvent {
  type: string;
  sequence_number: number;
  response_id: string;
  response?: AgentResponse;
}
```

Related: [AgentResponse](types.md#agentresponse).

## AgentResourceOptions

```typescript Definition theme={null}
export type AgentResourceOptions = Pick<
  AgentRequestOptions,
  "signal" | "timeoutMs"
>;
```

Related: [AgentRequestOptions](types.md#agentrequestoptions).

## AgentProject

```typescript Definition theme={null}
export interface AgentProject {
  id: string;
  name: string;
  color: string;
  createdAt: string;
}
```

## AgentProjectSummary

```typescript Definition theme={null}
export interface AgentProjectSummary
  extends AgentProject {
  chatCount: number;
  assetCount: number;
  attachedAssetCount: number;
  attachedCollectionCount: number;
  attachedCharacterCount: number;
  attachedResourceCount: number;
  assetsByType: {
    image: number;
    video: number;
    audio: number;
    "3d": number;
  };
  updatedAt: string;
  coverMedia: Array<{
    id: string;
    type: string;
    url: string;
  }>;
}
```

Related: [AgentProject](types.md#agentproject).

## AgentProjectConversation

```typescript Definition theme={null}
export interface AgentProjectConversation {
  id: string;
  title: string | null;
  mediaCount: number;
  updatedAt: string;
}
```

## AgentProjectResources

```typescript Definition theme={null}
export interface AgentProjectResources {
  attachedMedia: Array<{
    id: string;
    assetId: string;
    vectorId: string | null;
    type: string;
    source: string;
    url: string;
    title: string | null;
    prompt: string | null;
    createdAt: string;
  }>;
  collections: Array<Record<string, AgentJson>>;
  characters: Array<Record<string, AgentJson>>;
  smartEntities: Array<Record<string, AgentJson>>;
  generatedMedia: Array<Record<string, AgentJson>>;
}
```

Related: [AgentJson](types.md#agentjson).

## AgentProjectDocumentInput

```typescript Definition theme={null}
export interface AgentProjectDocumentInput {
  url: string;
  fileName: string;
  contentType: string;
  sizeBytes: number;
  text: string;
  pageCount?: number | null;
  truncated?: boolean;
}
```

## AgentProjectDocument

```typescript Definition theme={null}
export interface AgentProjectDocument {
  id: string;
  assetId: string;
  fileName: string;
  slug: string;
  url: string;
  contentType: string;
  sizeBytes: number;
  charCount: number;
  pageCount: number | null;
  truncated: boolean;
  summary: string | null;
  status: string;
  error: string | null;
  chunkCount: number;
  createdAt: string;
}
```

## AgentMemoryKind

```typescript Definition theme={null}
export type AgentMemoryKind =
  | "decision"
  | "fact"
  | "preference"
  | "style"
  | "entity";
```

## AgentProjectMemory

```typescript Definition theme={null}
export interface AgentProjectMemory {
  primer: string | null;
  primerUpdatedAt: string | null;
  notes: Array<{
    id: string;
    kind: AgentMemoryKind | null;
    content: string;
    pinned: boolean;
    sourceChatId: string | null;
    createdAt: string;
  }>;
}
```

Related: [AgentMemoryKind](types.md#agentmemorykind).

## AgentGenerationTask

```typescript Definition theme={null}
export type AgentGenerationTask =
  | "text-to-image"
  | "text-to-video"
  | "image-to-video"
  | "music"
  | "text-to-speech"
  | "sound-effects"
  | "image-to-3d";
```

## AgentGenerationPreferences

```typescript Definition theme={null}
export type AgentGenerationPreferences = {
  aspect_ratio?:
    | "1:1"
    | "16:9"
    | "9:16"
    | "4:3"
    | "3:4"
    | "3:2"
    | "2:3"
    | "21:9";
  resolution?: "720p" | "1080p" | "2K" | "4K";
  duration?: number;
  generate_audio?: boolean;
};
```

## AgentGenerationDefaults

A null override selects automatic behavior. Use `inherit` to restore inheritance. See [defaults](settings.md#set-generation-defaults).

```typescript Definition theme={null}
export type AgentGenerationDefaults = {
  preferences: {
    [K in keyof AgentGenerationPreferences]?:
      | AgentGenerationPreferences[K]
      | null;
  };
  preferredModels: Partial<
    Record<AgentGenerationTask, string | null>
  >;
};
```

Related: [AgentGenerationPreferences](types.md#agentgenerationpreferences), [AgentGenerationTask](types.md#agentgenerationtask).

## AgentGenerationSettings

Read before updating and preserve unrelated fields. See [settings](settings.md#change-generation-settings) for field meaning and revision handling.

```typescript Definition theme={null}
export type AgentGenerationSettings = {
  version: 1;
  revision: number;
  groups: Partial<
    Record<
      AgentGenerationTask,
      {
        model?: string;
        fields: Record<
          string,
          {
            value:
              | string
              | number
              | boolean
              | string[]
              | {
                  width: number;
                  height: number;
                };
            sourceEndpointId: string;
            label: string;
          }
        >;
      }
    >
  >;
  preferences?: AgentGenerationPreferences;
  preferredModels?: Partial<
    Record<AgentGenerationTask, string>
  >;
  preferredModelOverrides?: AgentGenerationDefaults["preferredModels"];
  defaults?: AgentGenerationDefaults;
  reviewBeforeGenerating: boolean;
};
```

Related: [AgentGenerationTask](types.md#agentgenerationtask), [AgentGenerationPreferences](types.md#agentgenerationpreferences), [AgentGenerationDefaults](types.md#agentgenerationdefaults).

## AgentGenerationSettingsOverrides

```typescript Definition theme={null}
export type AgentGenerationSettingsOverrides = {
  groups?: Partial<
    Record<
      AgentGenerationTask,
      {
        model?: string | null;
        fields?: Record<
          string,
          {
            value:
              | string
              | number
              | boolean
              | string[]
              | {
                  width: number;
                  height: number;
                };
            sourceEndpointId: string;
            label: string;
          } | null
        >;
      }
    >
  >;
  preferences?: AgentGenerationDefaults["preferences"];
  preferredModels?: AgentGenerationDefaults["preferredModels"];
  reviewBeforeGenerating?: boolean;
};
```

Related: [AgentGenerationTask](types.md#agentgenerationtask), [AgentGenerationDefaults](types.md#agentgenerationdefaults).

## AgentDefaultsTarget

```typescript Definition theme={null}
export type AgentDefaultsTarget =
  | {
      scope: "personal";
    }
  | {
      scope: "project";
      projectId: string;
    }
  | {
      scope: "chat";
      chatId: string;
    };
```

## AgentDefaultsUpdate

```typescript Definition theme={null}
export type AgentDefaultsUpdate = {
  changes?: AgentGenerationDefaults;
  inherit?: {
    preferences?: Array<
      keyof AgentGenerationPreferences
    >;
    preferredModels?: AgentGenerationTask[];
  };
  expectedLocal: AgentGenerationDefaults;
};
```

Related: [AgentGenerationDefaults](types.md#agentgenerationdefaults), [AgentGenerationPreferences](types.md#agentgenerationpreferences), [AgentGenerationTask](types.md#agentgenerationtask).

## AgentDefaultsSource

```typescript Definition theme={null}
export type AgentDefaultsSource =
  | "personal"
  | "project"
  | "chat"
  | "generation"
  | "auto";
```

## AgentDefaultsSources

```typescript Definition theme={null}
export type AgentDefaultsSources = {
  preferences: Partial<
    Record<
      keyof AgentGenerationPreferences,
      AgentDefaultsSource
    >
  >;
  preferredModels: Partial<
    Record<AgentGenerationTask, AgentDefaultsSource>
  >;
};
```

Related: [AgentGenerationPreferences](types.md#agentgenerationpreferences), [AgentDefaultsSource](types.md#agentdefaultssource), [AgentGenerationTask](types.md#agentgenerationtask).

## AgentDefaultsView

```typescript Definition theme={null}
export type AgentDefaultsView = {
  local: AgentGenerationDefaults;
  inherited: AgentGenerationSettings;
  effective: AgentGenerationSettings;
  sources: AgentDefaultsSources;
  inheritedSources: AgentDefaultsSources;
  revision: number;
  projectId: string | null;
};
```

Related: [AgentGenerationDefaults](types.md#agentgenerationdefaults), [AgentGenerationSettings](types.md#agentgenerationsettings), [AgentDefaultsSources](types.md#agentdefaultssources).

## AgentModel

```typescript Definition theme={null}
export type AgentModel = {
  id: string;
  modelId: string;
  title: string;
  category: string;
  shortDescription: string;
  thumbnailUrl: string | null;
  modelLabId?: string;
  isFavorited: boolean;
};
```

## AgentModelCapabilities

```typescript Definition theme={null}
export type AgentModelCapabilities = {
  endpointId: string;
  label: string;
  category: string;
  modelLabId?: string;
  fields: Array<{
    name: string;
    label: string;
    type: AgentJson;
    description: string;
    required: boolean;
    defaultValue?: string | number | boolean;
    [key: string]: AgentJson | undefined;
  }>;
};
```

Related: [AgentJson](types.md#agentjson).

## AgentPreferences

Update one section at a time. See [preferences](settings.md#update-account-preferences) for meanings, defaults, and permissions.

```typescript Definition theme={null}
export type AgentPreferences = {
  general: {
    preferredName: string;
    profession:
      | "product_management"
      | "engineering"
      | "human_resources"
      | "finance"
      | "marketing"
      | "sales"
      | "operations"
      | "data_science"
      | "design"
      | "legal"
      | "other"
      | null;
    defaultModel: string | null;
    liveVoice?:
      | "quartz"
      | "ripple"
      | "vesper"
      | "willow"
      | "stone"
      | "gleam"
      | "meridian"
      | "bossa"
      | "tempo"
      | "beacon"
      | "delta"
      | "cinder"
      | null;
    sequencerEnabled?: boolean;
  };
  cost: {
    confirmImage: boolean;
    confirmVideo: boolean;
    confirmAudio: boolean;
    confirm3d: boolean;
    alwaysConfirmAudio: boolean;
    alwaysConfirm3d: boolean;
    safetyCapUsd: number;
  };
  skills: {
    enabled: boolean;
    disabledPresetNames: string[];
    disabledFalSkillIds: string[];
    disabledSkillIds: string[];
  };
  notifications: {
    turnComplete: boolean;
  };
  preferredModels?: Partial<
    Record<AgentGenerationTask, string>
  >;
  generationPreferences?: AgentGenerationPreferences;
};
```

Related: [AgentGenerationTask](types.md#agentgenerationtask), [AgentGenerationPreferences](types.md#agentgenerationpreferences).

## AgentQueueItem

```typescript Definition theme={null}
export type AgentQueueItem = {
  turnId: string;
  qlane: "user" | "drawer";
  kind: "prompt" | "continuation";
  prompt?: string;
  stepLabel?: string;
  position: number;
  requiresApproval: boolean;
  parked: boolean;
  halted: boolean;
  planStep?: {
    planBlockId: string;
    planExecutionId: string;
    planStepId: string;
    planStepOrder: number;
  };
};
```

## AgentQueueDispatch

```typescript Definition theme={null}
export type AgentQueueDispatch = {
  promoted: boolean;
  turnId?: string;
  assistantMessageId?: string;
  requeued?: boolean;
  reason?: string;
};
```

## AgentRunView

```typescript Definition theme={null}
export type AgentRunView = {
  operation: AgentOperation;
  artifacts: AgentArtifact[];
  input_requests: AgentInputRequest[];
};
```

Related: [AgentOperation](types.md#agentoperation), [AgentArtifact](types.md#agentartifact), [AgentInputRequest](types.md#agentinputrequest).

## AgentGenerationSummary

```typescript Definition theme={null}
export type AgentGenerationSummary = {
  counts: {
    image: number;
    video: number;
    audio: number;
    model3d: number;
  };
  costsNanoUsd: {
    image: number;
    video: number;
    audio: number;
    model3d: number;
  };
  models: Array<{
    endpointId: string;
    modality:
      | "image"
      | "video"
      | "audio"
      | "model3d"
      | null;
    count: number;
    costNanoUsd: number;
    title: string | null;
    modelLabId: string | null;
  }>;
  totalCount: number;
  totalCostNanoUsd: number;
  pricedRequestCount: number;
  unpricedRequestCount: number;
};
```

## AgentLibraryMediaType

```typescript Definition theme={null}
export type AgentLibraryMediaType =
  | "image"
  | "video"
  | "audio"
  | "3d";
```

## AgentLibraryEntityType

```typescript Definition theme={null}
export type AgentLibraryEntityType =
  | "character"
  | "prop"
  | "environment"
  | "style"
  | "scene";
```

## AgentLibraryEntityReference

```typescript Definition theme={null}
export interface AgentLibraryEntityReference {
  assetRecordId: string;
  url: string;
}
```

## AgentLibraryEntity

All five entity types use this shape. The `references` array contains defining images, not the associated-media gallery. See [entities](library.md#read-entity-metadata-and-references).

```typescript Definition theme={null}
export interface AgentLibraryEntity {
  id: string;
  userId: string;
  type: AgentLibraryEntityType;
  name: string;
  handle: string | null;
  description: string | null;
  metadata: AgentJson;
  thumbnailAssetId: string | null;
  thumbnailUrl: string | null;
  isFavorited: boolean;
  references: AgentLibraryEntityReference[];
  createdAt: string;
  updatedAt: string;
}
```

Related: [AgentLibraryEntityType](types.md#agentlibraryentitytype), [AgentJson](types.md#agentjson), [AgentLibraryEntityReference](types.md#agentlibraryentityreference).

## AgentLibraryEntityQuery

```typescript Definition theme={null}
export interface AgentLibraryEntityQuery {
  types?: AgentLibraryEntityType[];
  search?: string;
  limit?: number;
  offset?: number;
}
```

Related: [AgentLibraryEntityType](types.md#agentlibraryentitytype).

## AgentLibraryEntityInput

Characters require a description and do not accept custom metadata. All types require one to twenty defining reference images.

```typescript Definition theme={null}
export type AgentLibraryEntityInput = {
  name: string;
  handle?: string;
  referenceImages: string[];
  coverImageUrl?: string | null;
} & (
  | {
      type: "character";
      description: string;
      metadata?: never;
    }
  | {
      type: Exclude<
        AgentLibraryEntityType,
        "character"
      >;
      description?: string | null;
      metadata?: Record<string, AgentJson> | null;
    }
);
```

Related: [AgentLibraryEntityType](types.md#agentlibraryentitytype), [AgentJson](types.md#agentjson).

## AgentLibraryEntityUpdate

Omitted fields stay unchanged. A supplied reference list replaces the complete defining set. Types and character handles cannot change. Character metadata is managed automatically.

```typescript Definition theme={null}
export interface AgentLibraryEntityUpdate {
  name?: string;
  handle?: string | null;
  description?: string | null;
  metadata?: Record<string, AgentJson> | null;
  referenceImages?: string[];
  coverImageUrl?: string | null;
}
```

Related: [AgentJson](types.md#agentjson).

## AgentLibraryTag

```typescript Definition theme={null}
export interface AgentLibraryTag {
  id: string;
  name: string;
  color: string;
  createdAt: string;
}
```

## AgentCharacterInput

```typescript Definition theme={null}
export interface AgentCharacterInput {
  name: string;
  description: string;
  referenceImages: string[];
  coverImageUrl?: string | null;
}
```

## AgentCharacterReference

```typescript Definition theme={null}
export interface AgentCharacterReference {
  assetRecordId: string | null;
  assetId: string | null;
  url: string;
  isCover?: boolean;
}
```

## AgentLibraryAsset

Use `assetRecordId` for library writes. It can be absent or null. Other IDs are not substitutes.

```typescript Definition theme={null}
export interface AgentLibraryAsset {
  assetRecordId?: string | null;
  assetId: string | null;
  vectorId: string;
  requestId: string | null;
  url: string | null;
  type: AgentLibraryMediaType;
  title: string;
  endpoint: string | null;
  createdAt: string | null;
  source: string | null;
  prompt: string | null;
  width: number | null;
  height: number | null;
  size?: number | null;
  contentType: string | null;
  isFavorited: boolean;
  collectionIds: string[];
  tags?: Array<{
    id: string;
    name: string;
    color: string | null;
    createdAt: string;
  }>;
}
```

Related: [AgentLibraryMediaType](types.md#agentlibrarymediatype).

## AgentLibraryAssetQuery

```typescript Definition theme={null}
export interface AgentLibraryAssetQuery {
  q?: string;
  searchImageUrl?: string;
  searchVideoUrl?: string;
  mediaTypes?: AgentLibraryMediaType[];
  sources?: Array<"upload" | "response">;
  section?:
    | "all-media"
    | "generated"
    | "uploads"
    | "favorites";
  endpoints?: string[];
  collectionId?: string | null;
  recursive?: boolean;
  characterSearchIdentifiers?: string[];
  assetRecordIds?: string[];
  tagIds?: string[];
  tagMode?: "any" | "all";
  sortOrder?: "newest" | "oldest";
  cursor?: string | null;
  limit?: number;
}
```

Related: [AgentLibraryMediaType](types.md#agentlibrarymediatype).

## AgentCollectionFilter

Semantic filters require exactly one query input. Filter depth cannot exceed three levels. See [smart collections](library.md#create-a-smart-collection).

```typescript Definition theme={null}
export type AgentCollectionFilter =
  | {
      and: AgentCollectionFilter[];
    }
  | {
      or: AgentCollectionFilter[];
    }
  | {
      field:
        | "endpoint"
        | "status"
        | "type"
        | "source"
        | "created_at";
      op:
        | "eq"
        | "neq"
        | "in"
        | "gt"
        | "gte"
        | "lt"
        | "lte";
      value: string | number | string[] | number[];
    }
  | {
      semantic: {
        text?: string;
        image_url?: string;
        video_url?: string;
        min_similarity: number;
      };
    };
```

## AgentCollectionInput

```typescript Definition theme={null}
export interface AgentCollectionInput {
  name: string;
  description?: string;
  icon?: string;
  color?: string;
  coverImageUrl?: string;
  filters?: AgentCollectionFilter | null;
  parentCollectionId?: string | null;
}
```

Related: [AgentCollectionFilter](types.md#agentcollectionfilter).

## AgentLibraryCollection

```typescript Definition theme={null}
export interface AgentLibraryCollection {
  id: string;
  type: "manual" | "smart" | AgentLibraryEntityType;
  name: string;
  description: string | null;
  icon: string | null;
  color: string | null;
  coverImageUrl: string | null;
  filters: AgentCollectionFilter | null;
  parentCollectionId: string | null;
  characterIdentifier: string | null;
  referenceImageUrls?: string[];
  isFavorited: boolean;
  createdAt: string;
  updatedAt: string;
  assetCount: number | null;
  previewAssets: Array<{
    id: string;
    type: string;
    url: string;
    createdAt: string;
  }>;
}
```

Related: [AgentLibraryEntityType](types.md#agentlibraryentitytype), [AgentCollectionFilter](types.md#agentcollectionfilter).

## AgentSkillContent

```typescript Definition theme={null}
export interface AgentSkillContent {
  name: string;
  description: string;
  body: string;
  references?: Record<string, string>;
}
```

## AgentSkillSource

```typescript Definition theme={null}
export interface AgentSkillSource {
  repoUrl: string;
  ref?: string;
  subpath?: string;
}
```

## AgentSkill

```typescript Definition theme={null}
export interface AgentSkill
  extends AgentSkillContent {
  id: string;
  origin: "fal" | "user";
  references: Record<string, string>;
  assets: Record<string, string>;
  disabled: boolean;
  shadowed: boolean;
  attribution: string | null;
  sourceRepo: string | null;
  sourceCommitSha: string | null;
  sourcePath: string | null;
  installedAt: string | null;
  updatedAt: string | null;
}
```

Related: [AgentSkillContent](types.md#agentskillcontent).

## AgentInstalledSkill

```typescript Definition theme={null}
export interface AgentInstalledSkill
  extends AgentSkillContent {
  id: string;
  references: Record<string, string>;
  assets: Record<string, string>;
  metadata: Record<string, AgentJson>;
  allowedTools: string[] | null;
  sourceRepo: string | null;
  sourceCommitSha: string | null;
  sourcePath: string | null;
  installedAt: string;
  updatedAt: string;
}
```

Related: [AgentSkillContent](types.md#agentskillcontent), [AgentJson](types.md#agentjson).

## AgentSkillImportPreview

```typescript Definition theme={null}
export interface AgentSkillImportPreview {
  name: string;
  description: string;
  license?: string;
  sourceRepo: string;
  sourceCommitSha: string;
  sourcePath: string;
  hasScripts: boolean;
  referenceCount: number;
  assetCount: number;
  totalBytes: number;
  warnings: string[];
  nameClash: boolean;
  nameClashRepo: string | null;
  presetClash: boolean;
}
```

## AgentConversationSharePolicy

```typescript Definition theme={null}
export type AgentConversationSharePolicy = {
  id: string;
  policy: {
    version: 1;
    accounts: (
      | {
          id: string;
          allUsers: true;
        }
      | {
          id: string;
          emails: string[];
        }
    )[];
    emails: string[];
  } | null;
  expiresAt: string | null;
};
```

## AgentConversationSharingInput

```typescript Definition theme={null}
export type AgentConversationSharingInput = {
  allowedAccountIds?: string[];
  emails?: string[];
  scopedAccountId?: string;
  scopedEmails?: string[];
  expiresAt?: string | null;
};
```

## AgentProjectDocumentImport

```typescript Definition theme={null}
export interface AgentProjectDocumentImport {
  url: string;
  fileName: string;
  contentType?: string;
}
```
