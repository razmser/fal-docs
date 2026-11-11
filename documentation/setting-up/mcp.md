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

# fal MCP server

> Connect the fal MCP server to ChatGPT, Claude, Claude Code, Codex CLI, Cursor, Grok Bot, Pi, Replit, and other clients using OAuth, or install it with one command.

Connect your favorite app to fal and start making images, videos, and audio. The fal MCP server lets your assistant find models, check schemas and pricing, upload inputs, and run generations.

For a product overview and demos, see the official [fal MCP Server page](https://fal.ai/mcp).

Use the fal plugin in ChatGPT, Cursor, or Grok Bot, or connect an OAuth-compatible MCP client to:

```text theme={null}
https://mcp.fal.ai/mcp-relay
```

The transport is **Streamable HTTP** and authentication is **OAuth**. Sign in through your browser. You don't need to create an API key or add an authorization header for these connections.

> [!NOTE]
>
> This is the model-generation connection. The separate [Platform MCP](platform-mcp.md) provides account operations and serverless debugging. The [documentation MCP](#documentation-only-mcp) searches public documentation without accessing your account or generating media.

## Setup

Connect with browser sign-in using `https://mcp.fal.ai/mcp-relay`.

### Coding agents: one command

On macOS or Linux, run this command in your terminal:

```bash theme={null}
curl -fsSL https://fal.ai/mcp/install.sh | bash
```

The installer adds fal to each of these agents that it finds on your computer:

* Claude Code, Codex CLI, and Cursor
* VS Code and Devin Desktop (formerly Windsurf)
* Gemini CLI, Grok Build, and opencode

The installer skips agents that already have fal. It does not write an API key. If a config file has comments or trailing commas, the installer does not change it and prints the entry to add yourself. After it finishes, sign in once in each agent. The installer shows the sign-in step for each agent.

To set up a coding agent by hand, use its guide:

> [card] [Claude Code](mcp/claude-code.md)
>
> Add the remote server, then authenticate with /mcp

> [card] [Codex CLI](mcp/codex-cli.md)
>
> Add the server and run codex mcp login fal

> [card] [Cursor](mcp/cursor.md)
>
> Install from the Cursor Marketplace, or add the server URL

> [card] [Pi](mcp/pi.md)
>
> Add the server. Run `pi mcp login fal`.

If you set up fal from inside a coding agent, paste this message into the agent instead. The agent then uses its own MCP command, which it can run without approval for an install script:

```text theme={null}
Add the fal MCP server to this coding agent with its own MCP command, not an install script. The server is https://mcp.fal.ai/mcp-relay (remote, Streamable HTTP, OAuth sign-in). In Claude Code: claude mcp add --transport http --scope user fal https://mcp.fal.ai/mcp-relay. In Codex: codex mcp add fal --url https://mcp.fal.ai/mcp-relay. Then tell me how to sign in.
```

### Apps and chat agents

The installer cannot reach these. Connect fal inside each app.

> [card] [ChatGPT](mcp/chatgpt.md)
>
> Install the fal plugin and connect your account

> [card] [Claude](mcp/claude.md)
>
> Add a connector and sign in through your browser

> [card] [Grok Bot](mcp/grok-bot.md)
>
> Add the fal plugin and sign in through your browser

> [card] [Replit](mcp/replit.md)
>
> Open the install link and sign in

> [card] [Muse and Instinct](mcp/chat-agents.md)
>
> Ask the agent in chat to add fal

> [card] [Other tools](mcp/other-clients.md)
>
> Copy the URL into a Streamable HTTP and OAuth client

> [card] [genmedia CLI](genmedia.md)
>
> A separate terminal connection using a local API key

### Check the connection

Ask your assistant:

```text theme={null}
Use fal to search for image generation models. Do not run a model.
```

A successful connection returns model search results. If authentication fails, complete the client's browser sign-in and authorization. Never paste an OAuth token or API key into chat.

## Try your first generation

When fal tools are available, paste one of these prompts into your app:

> [tab] Image
>
> ```text theme={null}
> Use fal to create a warm, minimal image for a coffee subscription website. Choose a suitable model. Show the estimated cost. Ask for approval before generation. After approval, wait for completion. Return the image with a download link.
> ```

> [tab] Video
>
> ```text theme={null}
> Use fal to create a short cinematic video of ocean waves at sunset. Choose a suitable model. Show the estimated cost. Ask for approval before generation. After approval, wait for completion. Return the video with a download link.
> ```

> [tab] Audio
>
> ```text theme={null}
> Use fal to create a calm speech recording that says: Welcome to our website. Choose a text-to-speech model. Show the estimated cost. Ask for approval before generation. After approval, wait for completion. Return the audio with a download link.
> ```

Generations use fal credits. Confirm the estimated cost before approving generation. These prompts ask the assistant to request approval; they do not add a server-side approval gate.

## Active MCP account

In [MCP & connectors](https://fal.ai/dashboard/ai-tools), select the **Active MCP account**. Uploads and new generations use this account and its credits across your connected apps. You can also open [Account settings](https://fal.ai/dashboard/account/details), open an account's row menu, and choose **Use for MCP**.

Your personal account is the default until you choose another. The preference applies to new operations across your OAuth MCP connections, including the plugin. No reconnect or plugin reinstall is needed. The website's account switcher is separate.

Existing jobs stay with the account that started them. If access to the selected account fails, operations stop instead of using personal credits. API-key connections, including genmedia, keep using the account that owns their key.

## How it works

Your client discovers OAuth through the relay, opens browser sign-in, and uses the authorized connection to call fal tools. The relay resolves your active MCP account for new operations. Credentials should stay in the client's connection settings, outside prompts and project files.

The tools cover model discovery, generation, queue management, and uploads. Tool availability can change; use your client's tool list to see what is enabled.

## Documentation-only MCP

To search public guides and API references without connecting an account, use:

```text theme={null}
https://fal.ai/docs/mcp
```

This is a separate documentation server. It does not run models or use your fal credits. Connect the model-generation relay above when you want your assistant to generate media.

## Available tools

The model-generation server provides these tools:

| Tool | What it does |
| - | - |
| `search_models` | Search the model catalog by keyword or category |
| `get_model_schema` | Check a model's input and output parameters |
| `get_pricing` | Check model pricing before generation |
| `search_docs` | Search fal documentation |
| `recommend_model` | Find models for a task |
| `run_model` | Run a model with a bounded wait; return its result or a request ID to continue polling |
| `submit_job` | Submit a long-running job and return its request ID |
| `check_job` | Check an existing job |
| `get_job_result` | Retrieve a completed job's output |
| `cancel_job` | Cancel a queued or running job |
| `upload_file` | Prepare a signed upload or upload a public URL or base64 data |

***

## Tool Reference

### search\_models

Search fal's model catalog by keyword, category, or both.

**Parameters:**

| Parameter | Type | Description |
| - | - | - |
| `query` | string (optional) | Free-text search, e.g. `"flux"`, `"video generation"`, `"upscale"` |
| `category` | string (optional) | Filter by category: `text-to-image`, `image-to-video`, `text-to-video`, `text-to-speech`, `image-to-3d`, `image-editing`, `llm`, and more |
| `limit` | number (optional) | Max results to return (default 20, max 100) |
| `cursor` | string (optional) | Pagination cursor. Pass the `next_cursor` value from a previous `search_models` response to fetch the next page. |

**Example response:**

```json theme={null}
{
  "models": [
    {
      "endpoint_id": "fal-ai/flux/dev",
      "name": "FLUX.1 [dev]",
      "category": "text-to-image",
      "description": "State-of-the-art text-to-image model"
    }
  ],
  "total_shown": 1,
  "has_more": true,
  "next_cursor": "eyJvZmZzZXQiOjIwfQ"
}
```

***

### get\_model\_schema

Get the full input/output schema for a specific model. Use this before `run_model` to understand what parameters are accepted.

**Parameters:**

| Parameter | Type | Description |
| - | - | - |
| `endpoint_id` | string | The model ID, e.g. `"fal-ai/flux/dev"` |

***

### run\_model

Run a fal model through the queue. The server waits up to 45 seconds by default. It returns `completed` with the result or `processing` with a request ID and URLs to check the job.

**Parameters:**

| Parameter | Type | Description |
| - | - | - |
| `endpoint_id` | string | The model ID, e.g. `"fal-ai/flux/dev"` |
| `input` | object | Model parameters as JSON. Use `get_model_schema` to see accepted fields. |
| `expiration_seconds` | number (optional) | Positive CDN lifetime in seconds for generated media. Omit to use the account default. |
| `store_payload` | boolean (optional) | Set to `false` to disable request payload storage in fal and omit `recipe.input` from the response. See [Response recipe](#response-recipe). |

**Example:**

```json theme={null}
{
  "endpoint_id": "fal-ai/flux/dev",
  "input": {
    "prompt": "a photorealistic mountain landscape at sunset",
    "image_size": "landscape_16_9"
  }
}
```

> [!NOTE]
>
> For long-running models (video, 3D, training), use `submit_job` → `check_job` → `get_job_result` from the start.
>
> If `run_model` returns `processing`, keep its `request_id` and pass its `status_url` to `check_job`. Respect `poll_after_seconds` when present. Once `check_job` returns `status: "COMPLETED"`, call `get_job_result` with the returned `response_url`. Do not resubmit to check progress: another `run_model` or `submit_job` call creates a new billable job.

***

### submit\_job

Submit a job without waiting for the result. Returns immediately with a `request_id` you can use with `check_job`.

**Parameters:**

| Parameter | Type | Description |
| - | - | - |
| `endpoint_id` | string | The model ID |
| `input` | object | Model parameters as JSON |
| `expiration_seconds` | number (optional) | Positive CDN lifetime in seconds for generated media. Omit to use the account default. |
| `store_payload` | boolean (optional) | Set to `false` to disable request payload storage in fal and omit `recipe.input` from the response. See [Response recipe](#response-recipe). |

***

### Response recipe

Both `run_model` and `submit_job` return a `recipe` object with the endpoint ID
and submitted inputs. These response excerpts show how `store_payload` changes
that object.

```json Default theme={null}
{
  "recipe": {
    "endpoint_id": "fal-ai/flux/dev",
    "input": { "prompt": "a mountain lake" }
  }
}
```

```json store_payload=false theme={null}
{
  "recipe": {
    "endpoint_id": "fal-ai/flux/dev",
    "input_omitted": true
  }
}
```

With `store_payload: false`, `recipe.input` is absent and `recipe.input_omitted`
is `true`.

***

### check\_job

Check a job's status. Use `get_job_result` to fetch the output or `cancel_job` to cancel it.

**Parameters:**

| Parameter | Type | Description |
| - | - | - |
| `endpoint_id` | string | The model ID |
| `request_id` | string | The request ID from `run_model` or `submit_job` |
| `status_url` | string (optional) | Status URL returned by `submit_job`, or by `run_model` when it returns `processing`. Used instead of deriving it. |

***

### get\_job\_result

Fetch the result of a completed job.

**Parameters:**

| Parameter | Type | Description |
| - | - | - |
| `endpoint_id` | string | The model ID |
| `request_id` | string | The request ID from `run_model` or `submit_job` |
| `response_url` | string (optional) | Response URL returned by `submit_job`, or by `run_model` when it returns `processing`. Used instead of deriving it. |

***

### cancel\_job

Cancel a queued or running job.

**Parameters:**

| Parameter | Type | Description |
| - | - | - |
| `endpoint_id` | string | The model ID |
| `request_id` | string | The request ID from `run_model` or `submit_job` |
| `cancel_url` | string (optional) | Cancel URL returned by `submit_job`, or by `run_model` when it returns `processing`. Used instead of deriving it. |

***

### upload\_file

Upload a file to fal's CDN for use as model input. Choose a signed local upload with `prepare_upload`, a public `url`, or complete base64 `data`.

**Parameters:**

| Parameter | Type | Description |
| - | - | - |
| `prepare_upload` | boolean (optional) | Return a signed upload URL for a client-side upload |
| `file_size` | number (optional) | Size in bytes, required with `prepare_upload`; 1 byte to 90 MB |
| `url` | string (optional) | Public HTTP(S) file URL |
| `data` | string (optional) | Complete base64-encoded file contents |
| `file_name` | string (optional) | Filename including extension. Required with `prepare_upload`; include it with `data` to identify the content type. |

For a local file up to 90 MB, call `upload_file` with `prepare_upload: true`, its basename as `file_name`, and its size as `file_size`. Upload the raw bytes from your client to the returned `upload_url` with HTTP PUT and the returned headers. Do not add an Authorization header or follow redirects; keep the signed upload URL private. Use `file_url` as model input only after the PUT succeeds. This flow uses your OAuth connection; no separate API key is needed.

Use base64 for small files (under 1 MB), or provide a public file URL. The hosted server cannot read a local path. An app must expose the attachment bytes or a URL before your assistant can upload it.

URL and base64 uploads return a `cdn_url` for model input (for example, `image_url` or `audio_url`). Prepared uploads return `upload_url` and `file_url`; use `file_url` only after a successful 2xx PUT.

***

### get\_pricing

Get model pricing before generation. Results can be cached for five minutes. If the response is throttled, respect `retry_after_seconds` before retrying.

**Parameters:**

| Parameter | Type | Description |
| - | - | - |
| `endpoint_id` | string | One model ID, or up to 50 comma-separated model IDs |

***

### recommend\_model

Describe what you want to create and get curated starting recommendations and model candidates. Candidate rank follows Explore discovery order; it is not a quality or speed score.

**Parameters:**

| Parameter | Type | Description |
| - | - | - |
| `task` | string | What you want to do, e.g. `"generate a photorealistic portrait"`, `"create a 10s cinematic video"`, `"remove background from an image"` |

***

### search\_docs

Search the fal documentation for guides, API references, and code examples.

**Parameters:**

| Parameter | Type | Description |
| - | - | - |
| `query` | string | What you're looking for, e.g. `"how to upload a file"`, `"queue API"`, `"LoRA training"` |

***

## FAQ

> [tab] My credits are on a team account
>
> Select the team as **Active MCP account** in [MCP & connectors](https://fal.ai/dashboard/ai-tools), or choose **Use for MCP** from its row menu in [Account settings](https://fal.ai/dashboard/account/details). The website account switcher is separate. API-key connections keep using the account that owns their key.

> [tab] What models can I use?
>
> Models in the [fal catalog](https://fal.ai/models) include image, video, audio, speech, 3D, and language models. Use `search_models` or `recommend_model` to find what you need.

> [tab] Do I need an API key?
>
> No for the fal plugin and OAuth MCP relay. Complete browser sign-in instead. SDK calls, genmedia CLI, and the separate Platform MCP still use their own authentication; OAuth instructions here do not replace those connections.

> [tab] Does it cost extra?
>
> No. The MCP server is free. You only pay for the fal model runs you trigger, at the same [pricing](https://fal.ai/pricing) as direct API calls.

> [tab] What about rate limits?
>
> The MCP server respects the same [concurrency limits](../model-apis/concurrency-limits.md) as direct API calls. Respect any throttling or retry guidance returned by the connection.
