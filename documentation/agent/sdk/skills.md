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

# Skills

> Discover, install, edit, and activate skills for fal Agent conversations.

Skills provide instructions and reference files for a task. The agent can select enabled skills automatically.
Use `fal.skills` when you want to activate a specific skill.

## Discover and activate a skill

```ts theme={null}
import { agent } from "./client.ts";

const skills = await agent.skills.list({ search: "product" });
const skill = skills.find((item) => !item.disabled && !item.shadowed);
if (skill) {
  const response = await agent.run({
    input: "Plan product photos for a blue ceramic mug.",
    fal: { skills: [skill.name] },
  });
  console.log(response.output_text);
}
```

Use skill names for activation and skill IDs for management.
A request accepts up to 50 skill names, each containing 1–100 characters.
Activation persists in the conversation. Later turns inherit activated skills.
An empty array does not clear existing activations.
Unavailable or disabled names are ignored. The account's master skills switch still applies.

`skills.list` accepts optional `search` and `conversationId` fields.
Results include fal skills and installed user skills, with `disabled` and `shadowed` flags.
A fal skill can shadow a user skill with the same name.
Use `skills.retrieve(id)` to read one visible skill, including its body and reference files.

## Import from GitHub

```ts theme={null}
import { agent } from "./client.ts";

const source = {
  repoUrl: "https://github.com/YOUR_ORG/YOUR_SKILLS",
  subpath: "skills/product-photos",
};
const preview = await agent.skills.previewImport(source);
console.log(preview.warnings);
```

The source accepts an optional Git ref and a subdirectory path.
Private repositories are not supported.
The preview reports the resolved commit, file counts, name conflicts, and unsupported scripts.
Previewing does not install the skill. Review the contents and warnings before installation.

```ts theme={null}
import { agent } from "./client.ts";

const skill = await agent.skills.importFromGithub({
  repoUrl: "https://github.com/YOUR_ORG/YOUR_SKILLS",
  ref: "REVIEWED_COMMIT_SHA",
  subpath: "skills/product-photos",
});
console.log(skill.id);
```

Scripts are not supported for installation. A conflicting user skill name blocks installation.
Importing the same repository and path updates the existing installation.
A conflicting fal skill produces a shadowing warning instead.

## Create and edit

```ts theme={null}
import { agent } from "./client.ts";

const { skill } = await agent.skills.create({
  name: "product-photos",
  description: "Plan consistent product photography.",
  body: "Use soft studio light. Keep the product color unchanged.",
});
console.log(skill.id);
```

Use `skills.save(id, content)` to replace a user skill's name, description, body, and references.
Names contain 1–64 lowercase letters, digits, or hyphens, without a leading or trailing hyphen.
Descriptions contain 1–1,024 characters. The body must not be empty.
Reference paths must identify text files under `references/`.
Saving an imported skill detaches it from GitHub. Repository updates are then unavailable for that skill.
You cannot edit or uninstall fal-provided skills through these methods.

## Enable, update, or uninstall

```ts theme={null}
import { agent } from "./client.ts";

const skill = await agent.skills.retrieve("SKILL_ID");
await agent.skills.setEnabled({
  origin: skill.origin,
  key: skill.id,
  enabled: true,
});
```

Disabling a skill preserves its installation. Uninstalling removes an owned user skill.
Use `skills.checkForUpdates(id)` for GitHub installations.
The result contains `currentSha`, `latestSha`, and `hasUpdate`.
Call `skills.update(id)` to fetch and apply the repository update.

Skill writes are sent once. After an uncertain network result, read the skill before repeating a mutation.
See the [method reference](methods.md#skills) for every method and its return type.
