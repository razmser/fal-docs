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

# Composer and references

> Reference models, generations, assets, characters, skills, and connected apps from one palette. Attach files, drag and drop, and paste.

The composer is where you talk to the agent. It supports references through the `@` palette, file attachments, drag and drop, and long-text paste.

## The `@` palette

Type `@` anywhere in the composer, or click the **+** button, to open the palette. It searches models, generations, characters, and skills at once, and it groups results into sections.

| Section | What it holds |
| :- | :- |
| **Attach files** | Upload images, video, audio, or ZIP archives |
| **Generations** | Named runs from this chat, to use as the input for the next step |
| **Models** | Pin a model, or pick several to compare |
| **Assets** | Your [fal Assets](../assets-and-characters.md) library |
| **Characters** | Reusable identities |
| **Skills** | Force a [skill](../skills.md) for this turn |
| **Connectors** | A [connected app](../tools/connectors.md) |

![The composer palette with its sections: Attach files, Generations, Models, Assets, Characters, Skills, and Connectors](https://mintcdn.com/fal-d8505a2e/DmEWlE6WV9vaz0Me/images/agent/composer-palette.png?fit=max\&auto=format\&n=DmEWlE6WV9vaz0Me\&q=85\&s=f8e482d9169b2fca5a2da6fc2ec4711c)

Use the arrow keys to move, `Enter` to select, `Backspace` to go back to the list, and `Escape` to close. A picked item becomes a chip in the composer.

### Inside a section

Each section opens as a list with its own search. **Generations** shows the named runs from this chat with the model that made them. **Models** lists models with a modality filter. **Skills** lists every enabled skill.

![The Generations section of the palette listing two named runs with their model and media type](https://mintcdn.com/fal-d8505a2e/DmEWlE6WV9vaz0Me/images/agent/palette-generations.png?fit=max\&auto=format\&n=DmEWlE6WV9vaz0Me\&q=85\&s=51de3c4987785d4e8ad61c3a5d0d4604)

![The Models section of the palette with a modality filter and a list of models tagged by task](https://mintcdn.com/fal-d8505a2e/DmEWlE6WV9vaz0Me/images/agent/palette-models.png?fit=max\&auto=format\&n=DmEWlE6WV9vaz0Me\&q=85\&s=22d8078a91362c9ca3901e6567e3dd36)

![The Skills section of the palette listing enabled skills with their descriptions](https://mintcdn.com/fal-d8505a2e/DmEWlE6WV9vaz0Me/images/agent/palette-skills.png?fit=max\&auto=format\&n=DmEWlE6WV9vaz0Me\&q=85\&s=bf15e016aaa8d9af746f05beb2638405)

A character chip carries the identity into the prompt, so you do not describe the character again.

### Generation references

Every completed output gets an ordinal in the order it landed. When the agent names a run, it also gets a readable name, such as `cdg-bag-environments/open-field`. Both appear in the media viewer header. Pick a generation from the palette when you want an output as the input for the next step:

```text theme={null}
Upscale the open-field shot and then animate it with a slow push-in.
```

### Model pins

The model chip under the composer shows **Auto** when no model is pinned. Pick a model from the palette to pin it for the message. Remove the chip to return to Auto. See [Models and generation](../models-and-generation.md).

## Attachments

Click the attachment button or drag files into the chat. The agent uses attachments as references, edit sources, or context.

| Type | Formats | Size limit |
| :- | :- | :- |
| Image | JPEG, PNG, WebP, GIF | 50 MB |
| Video | MP4, WebM, QuickTime | 500 MB |
| Audio | MP3, WAV, OGG, FLAC, M4A, AAC | 50 MB |
| Document | PDF, DOCX, TXT, Markdown, CSV, TSV, JSON | 25 MB |
| Code and text | YAML, XML, HTML, CSS, JS, TS, Python, Go, Rust, Java, Ruby, shell scripts | 25 MB |
| Archive | ZIP, for training datasets | 2 GB |

A message can carry up to 20 attachments. Uploads retry automatically. Drafts, including attachments, are saved in the browser and survive a reload.

> [!NOTE]
>
> 3D files cannot be attached to a chat message. When you drop them on a project, they are saved to the project only.

## Drag and drop

Where you drop a file decides where it goes.

| Drop target | Result |
| :- | :- |
| Chat body | The file attaches to the message |
| Media rail, scope **This chat** | The file attaches to the message and is saved to the project library |
| Media rail, scope **Project** | The file is saved to the project. A dialog asks **Attach to this chat too?** |
| Project page | Documents become project context. Media joins the project's Assets |

In a chat without a project, files always attach to the message. ZIP files always attach to the chat and never enter a project library.

## Paste

* A pasted **fal media URL** becomes an attachment with a link chip.
* Any other pasted **URL** becomes a link chip. The URL stays in the message so the agent can read the page.
* Pasted **text** longer than 2,000 characters or 25 lines becomes a document attachment named `Pasted text.txt`.

## Send or queue

`Enter` sends the message. If the agent is busy, the message waits and runs next. `Alt+Enter` adds the message to the queue instead, so it runs after the current work. See [Queue and steering](queue.md).
