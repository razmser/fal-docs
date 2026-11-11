> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](quickstart.md) and the [Model APIs overview](model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Introduction to fal Agent

> fal Agent is an AI creative partner that plans and runs generative media work across every top image, video, audio, and 3D model on fal.

fal Agent is a conversational creative partner. You describe what you want to make. The agent chooses the models, writes the prompts, and runs the generations. It keeps your references consistent from one step to the next. It works across the full fal model catalog. One chat can move from a still image to a video, to a sound bed, and to a 3D asset without a change of tool.

fal Agent runs in the browser at [fal.ai/agent](https://fal.ai/agent). It is a product for creators, studios, and teams who want production-grade results without hand-picking a model for every step.

> [!NOTE]
>
> fal Agent is in Early Access. Access is enabled by default for signed-in
> accounts. You do not need a credit plan or an Enterprise contract to use fal
> Agent. Account and organization restrictions still apply. See [Access and
> pricing](agent/access-and-pricing.md).

## What fal Agent does

* **Chooses and runs models.** The agent picks the model for the task, writes the inputs, and repairs failed runs. You can pin a model when you want a specific one.
* **Plans multi-step work.** Larger requests become an editable plan card. You can rename steps, reorder them, pin models, and add approval checkpoints before anything runs.
* **Keeps context.** Projects hold chats, media, documents, characters, and a shared memory. The agent recalls decisions and preferences across sessions.
* **Edits with code.** A Python sandbox with ffmpeg, Pillow, and a video compositing renderer handles deterministic edits, captions, and cuts.
* **Reads the web and video.** The agent can search the web, read pages, download public media, and answer questions about a video with timestamps.
* **Arranges video sequences.** Clips become an editable timeline with audio layers. You export the final cut as one file.
* **Follows skills.** Built-in skills from fal encode creative guidelines for characters, cinematography, UGC ads, photo editing, and more. You can write your own.

## How a turn works

Every message you send starts a turn. The agent reasons about the request, calls tools, and streams its work into the chat.

**1. Understand**

The agent reads your message, your attachments, the active skills, and the
project memory. If the request is ambiguous, it can ask a short
multiple-choice question.

**2. Plan**

For multi-step work, the agent renders a plan card. You can edit the plan or
run it as written. Simple requests skip this step.

**3. Run**

Each generation is a normal fal model run. The agent prepares the input and
submits the run to the fal queue. Results stream into the chat and the media
rail as they land.

**4. Continue**

When a run lands, the agent can continue with the next step automatically.
Every remaining step is visible in the queue, and you can stop, reorder, or
edit it at any time.

## What you pay for

Model runs are billed as normal fal usage against your credits. Nothing else is billed. The agent's reasoning, the sandbox, web search, and video understanding are free to use for now. A spend chip next to the composer shows the running total for the chat, and [spending caps](agent/spending-caps.md) ask for confirmation before expensive runs.

## Start here

> [card] [Quickstart](agent/quickstart.md)
>
> Run your first chat in five minutes

> [card] [Access and pricing](agent/access-and-pricing.md)
>
> Optional credit plans, access restrictions, and what is billed

> [card] [Plan cards](agent/plan-cards.md)
>
> Edit, approve, and version the agent's plan

> [card] [Projects](agent/projects.md)
>
> Shared memory, documents, and media across chats

> [card] [Skills](agent/skills.md)
>
> Built-in creative guidelines and your own skills

> [card] [Tools](agent/tools/sandbox.md)
>
> Sandbox, web, video understanding, sequences, and more
