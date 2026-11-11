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

# Use LLMs Tutorial

> fal provides an easy-to-use API for generating text using Language Models (LLMs). You can use the `fal-ai/any-llm` endpoint to generate text based on a given prompt and model.

Here’s an example of how to use the `fal-ai/any-llm` endpoint to generate text using the `anthropic/claude-3.5-sonnet` model:

```js theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/any-llm", {
  input: {
    model: "anthropic/claude-3.5-sonnet",
    prompt: "What is the meaning of life?"
  },
});
```

## How to select LLM model to use

fal offers a variety of LLM models. You can select the model that best fits your needs based on the style and quality of the text you want to generate. Here are some of the available models:

* `anthropic/claude-3.5-sonnet`: Claude 3.5 Sonnet
* `google/gemini-pro-1.5`: Gemini Pro 1.5
* `meta-llama/llama-3.2-3b-instruct`: Llama 3.2 3B Instruct
* `openai/gpt-4o`: GPT-4o

To select a model, simply specify the model ID in the `model` field as shown in the example above. You can find more LLMs in the [Any LLM](https://fal.ai/models/fal-ai/any-llm) page.
