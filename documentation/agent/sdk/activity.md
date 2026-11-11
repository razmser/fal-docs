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

# Tool activity

> Read tool progress, visible reasoning, and activated skills from response snapshots.

Response snapshots include activity from their execution turns.
Use `fal.activities` to display progress and `fal.activated_skills` to inspect loaded skills.

```ts theme={null}
import { agent } from "./client.ts";

for await (const response of agent.stream({
  input: "Plan a product photo.",
})) {
  for (const activity of response.fal.activities ??
    []) {
    console.log(
      activity.id,
      activity.label,
      activity.status,
    );
  }
  console.log(response.fal.activated_skills ?? []);
}
```

Each update contains the complete activity snapshot. Replace displayed state instead of appending every update.
Activity IDs remain stable when you retrieve or reconnect to the same response.

| Field | Meaning |
| - | - |
| `id` | Stable activity identity within the response. |
| `turn_id` | Execution turn that produced the activity. |
| `kind` | `tool` or `reasoning`. |
| `label` | Display label. |
| `status` | `running` or `completed`. |
| `started_at`, `ended_at` | Unix timestamps in milliseconds. The end time is optional. |
| `duration_ms` | Optional duration in milliseconds. |

Tool activities include `node`, an optional tool `name`, and optional JSON `arguments` and `output`.
Reasoning activities include visible reasoning in `content`.
Check the activity kind before reading its fields.

Use structured [input requests](inputs.md) for questions and approvals.
Read the response status and error to determine whether the task succeeded.

`activated_skills` contains names loaded during these turns, including active skills inherited from the conversation.
It does not list every available skill. See [skills](skills.md) for discovery and activation.

Both arrays are optional. Missing fields mean the response does not report tool or skill activity.
