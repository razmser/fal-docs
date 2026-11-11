> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../documentation/quickstart.md) and the [Model APIs overview](../../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# fal api

> Call any fal model from the command line with fal api. Pass input as key=value pairs, use := for numbers and booleans, and stream output from /stream endpoints.

Call any model on fal directly from the command line. Useful for quick testing without writing code.

## Usage

```bash theme={null}
fal api <model_id> [key=value ...]
```

**Arguments:**

| Argument | Description |
| - | - |
| `model_id` | The model endpoint to call (e.g., `fal-ai/flux/schnell`) |
| `params` | Key-value pairs for the request body (e.g., `prompt="a sunset"`) |

## Examples

**Generate an image:**

```bash theme={null}
fal api fal-ai/flux/schnell prompt="a cat wearing sunglasses"
```

The CLI shows real-time status updates (queued, in progress) and logs while the request is processing, then prints the result.

**Nested parameters:**

Use bracket notation for nested values:

```bash theme={null}
fal api fal-ai/flux/schnell prompt="a sunset" image_size[width]:=1280 image_size[height]:=720
```

Use `:=` for non-string values (numbers, booleans):

```bash theme={null}
fal api fal-ai/flux/schnell prompt="a sunset" num_images:=4 seed:=42
```

**Streaming endpoints:**

If the model ID ends with `/stream`, the CLI streams output as it's generated:

```bash theme={null}
fal api fal-ai/any-llm/stream prompt="What is the meaning of life?" model=google/gemini-flash-1.5
```

## How It Works

* For regular endpoints: submits via the queue, polls for status with live log display, then prints the result
* For `/stream` endpoints: connects via streaming and prints output as it arrives
* Uses your configured `FAL_KEY` for authentication
