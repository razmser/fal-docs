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

# genmedia CLI

> An agent-first command-line tool for running fal models from any terminal or AI agent

**genmedia** is a command-line tool that gives any terminal, script, or AI agent direct access to fal's 1,000+ models. In a terminal it prints a compact human-readable view; when its output is piped or `--json` is passed, every command emits structured JSON.

While the [MCP server](mcp.md) is the best fit for assistants that speak the Model Context Protocol, genmedia needs nothing but a shell — so it also works in CI/CD pipelines, shell scripts, and agents driven purely by shell commands.

> [!NOTE]
>
> This is not the `fal` CLI. For deploying and managing your own fal applications — `fal deploy`, `fal run`, `fal apps` — see the [CLI Reference](../../api-reference/cli.md).

## Installation

> [tab] Linux and macOS
>
> ```bash theme={null}
> curl https://genmedia.sh/install -fsS | bash
> ```

> [tab] Windows
>
> ```powershell theme={null}
> irm https://genmedia.sh/install.ps1 | iex
> ```

Use the official installation commands from the [genmedia CLI repository](https://github.com/fal-ai-community/genmedia-cli#install). Installers use `~/.genmedia` on macOS/Linux and `%LOCALAPPDATA%\genmedia` on Windows, and add genmedia to your PATH.

> [!NOTE]
>
> genmedia requires a fal API key. If you don't have one yet, [create one here](https://fal.ai/dashboard/keys).

## Quick Start

**1. Add your key locally**

```bash theme={null}
genmedia setup
```

Enter your fal API key in the terminal. It is saved encrypted to your local config. If `FAL_KEY` already exists in your environment, you can use it instead. Keep keys out of chat. genmedia currently uses API-key authentication; it does not use the MCP relay's browser sign-in.

**2. Add optional skills**

If neither `.agents` nor `.claude` exists in your project, create `.agents` first. Run `genmedia init` from the project, then start a new coding-agent session.

**3. Check access**

```bash theme={null}
genmedia models --limit 1
```

**4. Generate an image**

```bash theme={null}
genmedia run fal-ai/flux/schnell --prompt "a mountain lake at sunset"
```

***

## Commands

genmedia covers configuration, model discovery, execution, file uploads, documentation search, and agent onboarding. Run `genmedia --help --json` for the complete machine-readable command list.

### setup

Interactive wizard that configures your API key (saved encrypted locally), `.env` auto-loading, default output mode, and automatic updates.

```bash theme={null}
genmedia setup
```

For agents and CI, configure it without prompts. Every flag is optional and omitted fields keep their current values, so repeated invocations are idempotent.

```bash theme={null}
genmedia setup --non-interactive --api-key "$FAL_KEY"
genmedia setup --non-interactive --output-format json --no-auto-load-env
```

**Options:**

| Flag | Description |
| - | - |
| `--non-interactive`, `-y` | Skip all prompts. Required when there is no TTY. |
| `--api-key <key>` | API key to save. Pass `""` to clear the saved key. |
| `--no-save-key` | With `--api-key`, don't persist the key — use `FAL_KEY` at runtime instead. |
| `--output-format <auto\|json\|standard>` | Default output mode. |
| `--auto-load-env` / `--no-auto-load-env` | Toggle auto-loading `FAL_KEY` from a project `.env`. |
| `--auto-update` / `--no-auto-update` | Toggle background update checks. |

***

### models

Search and inspect fal's catalog. Pass a query, a category filter, or both. When you give a query without `--category`, genmedia classifies it and applies an inferred category for you.

```bash theme={null}
# Search by keyword
genmedia models "video generation"

# Filter by category
genmedia models --category text-to-image

# Limit results
genmedia models "upscale" --limit 5

# Fetch specific models by ID
genmedia models --endpoint_id fal-ai/flux/dev,fal-ai/flux/schnell
```

**Options:**

| Flag | Description |
| - | - |
| `--category` | Filter by category, e.g. `text-to-image`, `image-to-video`, `text-to-speech`, and more |
| `--status` | `active` (default), `deprecated`, or `all` |
| `--limit` | Max results (default: 20) |
| `--cursor` | Pagination cursor from a previous response |
| `--endpoint_id` | Fetch specific models by ID — comma-separated or repeated |
| `--expand` | Expand additional fields: `openapi-3.0`, `enterprise_status` |

***

### schema

Get the full input/output parameters for any model. Use this before `run` to see what options are available.

```bash theme={null}
genmedia schema fal-ai/flux/dev

# Full OpenAPI JSON
genmedia schema fal-ai/flux/dev --format openapi
```

Returns all parameters with types, descriptions, defaults, and which are required.

***

### run

Run any fal model. By default, submits to the queue and waits for the result.

```bash theme={null}
# Generate an image
genmedia run fal-ai/flux/dev --prompt "a futuristic cityscape" --image_size landscape_16_9

# Save the output files to disk
genmedia run fal-ai/flux/dev --prompt "a cat" --download

# Submit without waiting
genmedia run fal-ai/kling-video/v3/pro/text-to-video \
  --prompt "a cat playing piano" --async
```

**Options:**

| Flag | Description |
| - | - |
| `--<param>` | Any model input parameter, e.g. `--prompt`, `--num_images` |
| `--async` | Submit to queue and return the request ID immediately |
| `--logs` | Stream logs while the model runs (terminal mode only) |
| `--download [template]` | Download every media URL in the result. Accepts a path or a template with `{index}`, `{name}`, `{ext}`, `{request_id}`. Written paths appear in JSON output under `downloaded_files`. |

Model parameters are passed as `--key value` flags, and the key names match the model's input schema exactly. To see a specific model's parameters rendered as CLI flags:

```bash theme={null}
genmedia run fal-ai/flux/dev --help
```

You can also skip the endpoint ID entirely — pass a bare prompt and genmedia picks a suitable model for you:

```bash theme={null}
genmedia run "a golden retriever surfing a wave"
```

***

### status

Check the status of an async job, fetch the result, or cancel it.

```bash theme={null}
# Check status
genmedia status fal-ai/flux/dev abc123-request-id

# Get the result
genmedia status fal-ai/flux/dev abc123-request-id --result

# Cancel a job
genmedia status fal-ai/flux/dev abc123-request-id --cancel
```

**Options:**

| Flag | Description |
| - | - |
| `--result` | Fetch the completed result |
| `--logs` | Show logs verbosely |
| `--cancel` | Cancel the queued job |
| `--download [template]` | Same as on `run`. Implies `--result`. |

***

### upload

Upload a local file or URL to fal's CDN. Returns a URL you can pass to any model.

```bash theme={null}
# Upload a local file
genmedia upload ./my-image.png

# Upload from URL
genmedia upload https://example.com/photo.jpg
```

Returns a `cdn_url` like `https://v3b.fal.media/files/b/...` that works with any model parameter that accepts a URL.

***

### pricing

Get the cost of running a model.

```bash theme={null}
genmedia pricing fal-ai/flux/dev
```

***

### docs

Search the fal documentation for guides, API references, and examples.

```bash theme={null}
genmedia docs "how to use webhooks"
```

***

### gallery

Every successful `genmedia run` records its outputs into a per-session, self-contained HTML page under `~/.genmedia/gallery/sessions/`, aggregating every image, video, audio, and 3D asset produced in one agent session into a filterable grid.

```bash theme={null}
# Print the current session's gallery URL
genmedia gallery

# Open it in your browser
genmedia gallery open current

# List every recorded session
genmedia gallery list
```

Set `GENMEDIA_NO_GALLERY=1` to disable recording.

***

### init and skills

Install agent skills so coding agents in a project know how to use genmedia without reading `--help` first.

```bash theme={null}
# Install the default genmedia skill bundle
genmedia init

# Browse and manage the skill registry
genmedia skills list
genmedia skills install genmedia
```

`init` writes into `.agents/skills/` if the project has an `.agents/` directory, otherwise `.claude/skills/`. Commit that directory so teammates and other agents get the same context.

***

### version and update

```bash theme={null}
genmedia version

# Download and swap in the latest release
genmedia update

# Only check, don't download
genmedia update --check
```

Automatic background update checks are on by default. Set `GENMEDIA_NO_UPDATE=1` to disable them; `genmedia update` still works.

***

## Output and JSON

Output adapts to where it's going:

* **In a terminal** — a compact, human-readable view.
* **Piped, redirected, or with `--json`** — structured JSON.

```bash theme={null}
genmedia models "text to video" --json | jq '.models[]'
```

To make JSON the default everywhere, run `genmedia setup --non-interactive --output-format json`.

Errors are written to stderr as JSON with a non-zero exit code:

```json theme={null}
{
  "error": "Model not found: fal-ai/nonexistent"
}
```

Some errors carry an additional `details` object with context such as `request_id`, `status`, or `validation_errors`. Success responses vary by command but always include the relevant data as a JSON object.

***

## Examples

### End-to-end image generation

```bash theme={null}
# 1. Find a model
genmedia models "fast image generation" --limit 3

# 2. Check what parameters it accepts
genmedia schema fal-ai/flux/schnell

# 3. Generate an image and save it locally
genmedia run fal-ai/flux/schnell \
  --prompt "a photorealistic sunset over the ocean" \
  --image_size landscape_16_9 \
  --num_images 1 \
  --download
```

### Video generation (async)

```bash theme={null}
# 1. Submit a video generation job
genmedia run fal-ai/kling-video/v3/pro/text-to-video \
  --prompt "a cinematic drone shot over a mountain range" \
  --duration 5 \
  --async

# Returns: { "request_id": "abc123..." }

# 2. Check status
genmedia status fal-ai/kling-video/v3/pro/text-to-video abc123

# 3. Get the result when complete
genmedia status fal-ai/kling-video/v3/pro/text-to-video abc123 --result
```

### Image-to-video with file upload

```bash theme={null}
# 1. Upload your image
genmedia upload ./my-photo.jpg
# Returns: { "cdn_url": "https://v3b.fal.media/files/b/..." }

# 2. Use the CDN URL as input
genmedia run fal-ai/kling-video/v3/pro/image-to-video \
  --prompt "gentle camera zoom, leaves blowing in wind" \
  --start_image_url "https://v3b.fal.media/files/b/..." \
  --async
```

> [!NOTE]
>
> Parameter names are per-endpoint. This endpoint takes `start_image_url` (plus an optional `end_image_url`), not `image_url`. Run `genmedia run <endpoint_id> --help` to see exactly what a model accepts.

### Use with AI agents

The fastest way to teach an agent about genmedia is to install the skill bundle in your project:

```bash theme={null}
genmedia init
```

For agents that build their own tool configuration, ask genmedia to describe itself:

```bash theme={null}
genmedia --help --json
```

This returns a structured object with the CLI's name, version, install info, expected environment variables, and a `commands` map giving each command's description, usage, arguments, options, and output fields — everything an agent needs to discover capabilities without guessing.

## Next Steps

> [card] [AI Tools](mcp.md)
>
> Connect MCP-compatible assistants to the full fal platform

> [card] [Get Your API Key](authentication.md)
>
> Create and manage the keys genmedia authenticates with

> [card] [Async Inference](../model-apis/inference/queue.md)
>
> How async submission, status polling, and webhooks work

> [card] [GitHub Repository](https://github.com/fal-ai-community/genmedia-cli)
>
> View the source code, report issues, or contribute
