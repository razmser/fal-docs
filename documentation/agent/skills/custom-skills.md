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

# Custom skills

> Write your own skill in the editor or install one from GitHub. Format, fields, references, updates, and limits.

A custom skill is a `SKILL.md` file with YAML frontmatter and a Markdown body. fal Agent follows the open format from [agentskills.io](https://agentskills.io), so skills written for other agents work here too.

## Create a skill

Open **Skills** and click **Install skill**. Two tabs:

* **Create your own.** Write the skill from scratch, or paste a `SKILL.md` you already have.
* **From GitHub.** Paste a URL to a public repository, or to a folder in one, that contains a `SKILL.md`. For example `https://github.com/owner/repo/tree/main/skill-name`.

Changes apply as soon as you save. The agent uses the skill in new chats.

## Format

```markdown theme={null}
---
name: brand-look
description: Apply the Acme brand look to every image. Use when the user mentions Acme, the brand kit, or a campaign asset.
---

## Palette
Teal #0F6E6E and rust #B4532A. Never pure black.

## Composition
Product centered, 15% margin, soft top light.

## Delivery
Always produce 1:1 and 4:5. Name runs `acme-<subject>`.
```

| Field | Required | Rule |
| :- | :- | :- |
| `name` | Yes | 1 to 64 characters. Lowercase letters, digits, and hyphens. Must start and end with a letter or digit. This is how the agent refers to the skill |
| `description` | Yes | Up to 1,024 characters. Tells the agent when to reach for the skill. Write it as a trigger, not a summary |
| `license`, `compatibility`, `metadata`, `allowed-tools` | No | Optional fields from the open format |

The body is free Markdown. Keep it focused. A body over about 5,000 tokens shows a warning, because a long skill crowds out the rest of the context.

### References

A skill can ship supporting files in a `references/` folder, such as example prompts or a style guide. The agent loads a reference file when the skill tells it to. Use the **References** field in the editor, or include the folder in the repository.

* Text files only. Binary files are skipped.
* Up to 1 MB of references and assets per skill. Files over the cap are skipped with a warning.
* `scripts/` folders are not supported and are rejected on import.
* `<script>` tags in the body are rejected.

## Edit a skill

Click a skill card to open the editor. The editor has fields for **Name**, **Description**, the **SKILL.md** body, and **References**. Saving is immediate. There is no draft or version history for custom skills.

### Skills installed from GitHub

A skill installed from GitHub keeps a link to its repository. Click **Update** on its card to pull the latest version from the default branch.

If you edit a GitHub skill in the editor, fal Agent asks **Detach from GitHub?** After you save, the skill is yours to maintain. **Update** is no longer available. Confirm with **Save and detach**.

## Names that match a built-in skill

If your skill has the same name as a fal skill, the fal skill takes precedence until you disable it on the Skills page. Your skill is saved either way, and it is marked as shadowed.

## Delete a skill

Open the skill and choose **Delete**. The agent no longer sees it. Chats that already activated it keep its text in their history.

## Limits

| Limit | Value |
| :- | :- |
| Skills per account | No limit |
| `description` length | 1,024 characters |
| References and assets per skill | 1 MB |
| GitHub sources | Public repositories only |

GitHub imports use unauthenticated requests. If you see a rate-limit message, wait a few minutes and try again.
