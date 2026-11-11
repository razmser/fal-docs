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

# Skills

> Skills are Markdown capabilities the agent picks up automatically based on the task. fal ships a set, and you can add your own.

A skill is a Markdown file that tells the agent how to do a kind of work well. It encodes the guidelines a good result needs: which models to prefer, how to structure prompts, what to check before delivering. Skills stack. A look, a format, and a delivery spec can all apply to one run.

Open **Skills** in the sidebar to browse them. Tabs show **All**, **Yours**, and **By fal**.

## Built-in skills

fal's creative engineers maintain these skills. They are on by default and appear under **By fal**.

| Skill | Use it for |
| :- | :- |
| **fal-video-generation** | The general router for video work. Make a clip from a description, animate a still, interpolate between two frames, restyle footage, extend or upscale a clip, reframe it, or add sound |
| **fal-video-production** | A finished short video ad from a brief. Script, shot list, one clip per shot, a pinned voice, a ducked music bed, and burned-in text, with a clip review before audio is paid for |
| **fal-script-writer** | A timed script and shot table before generation. Story beats or ad structure, duration and voiceover limits per shot, and continuity notes |
| **fal-shot-craft** | Wording the visual prompt of one video clip. Framing, camera, one settled state per clip, and specific gesture over named emotion |
| **fal-motion-graphics** | Animated on-screen graphics for a finished video. Titles, lower thirds, badges, end cards, and logo cards with entrance and exit motion |
| **video-compositing** | Captions, titles, animated logos, kinetic type, montages, and slideshows, rendered in the [sandbox](tools/sandbox.md) |
| **fal-audio-generation** | The router for audio. Voiceover and text to speech, voice replacement, dubbing, sound effects, music, and transcription |
| **h3-cinematic-prompter** | Finished prompts for the MiniMax Hailuo H3 video model. Produces prompt text only |
| **cinematography** | Shot language, camera movement, lighting, lens choice, color grade, film texture, and scene blocking |
| **character-design** | Original characters, reference sheets, expression sheets, outfit variations, identity-preserving edits, and character-to-video |
| **character-sheet** | One character design sheet, turnaround, or model sheet as a single image |
| **ugc** | Creator-style ads and social-proof videos. Talking-head scripts, product demos, testimonials, unboxing, and short vertical formats |
| **photo-editing** | Pixel-exact edits in the sandbox. Background removal, exposure, crop, straighten, format conversion, and compositing. Semantic edits go to a generative model |
| **fal-gamedev** | 2D pixel-art characters, sprite sheets, walk cycles, background removal, and parallax or isometric backgrounds |
| **wrap-it-up** | End-of-session handoff. Packages everything from the chat into one ZIP with a written progress document |
| **training** | Fine-tune a LoRA on your own images. Available to accounts with [training access](tools/training.md) |

![The Skills page showing the fal skills with their descriptions and enable toggles](https://mintcdn.com/fal-d8505a2e/DmEWlE6WV9vaz0Me/images/agent/skills-page.png?fit=max\&auto=format\&n=DmEWlE6WV9vaz0Me\&q=85\&s=d79b9dc437ae57a04970f2509e78b1a6)

## How a skill activates

* **Automatically.** The agent sees the name and description of every enabled skill. When a request matches, it loads the skill's full body.
* **On request.** Type `@` in the composer, open **Skills**, and pick one. It is loaded for that turn.

Once a skill is active in a chat, it stays active for the rest of the chat.

## Turn skills on and off

* Each skill card has a toggle. A disabled skill is invisible to the agent.
* **Settings → Skills → Enable skills** is the master switch. When it is off, the agent ignores all skills, including fal's and yours.

## Your own skills

You can write a skill in the editor or install one from a GitHub repository. See [Custom skills](skills/custom-skills.md).

> [card] [Custom skills](skills/custom-skills.md)
>
> Format, editor, GitHub install, updates, and limits
