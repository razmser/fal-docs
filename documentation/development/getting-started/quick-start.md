> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../quickstart.md) and the [Model APIs overview](../../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Quick Start: Deploy Your First fal App

> Install the fal CLI, write a Python app, test it with fal run, and deploy it with fal deploy. Get a persistent URL and a Playground in about two minutes.

This guide walks you through building and deploying a Hello World app on fal Serverless. By the end, you will have a working app running on cloud infrastructure with a persistent URL, a [Playground](../../model-apis/playground.md#your-own-apps) for browser-based testing, and client SDK code you can copy into any project. The entire process takes about two minutes.

The workflow you will learn here is the same one you will use for every fal app, from simple utilities to production AI models. You write a Python class, test it with `fal run`, deploy it with `fal deploy`, and call it with the [fal client SDK](../calling-your-endpoints.md). Once you are comfortable with this loop, the [Deploy Your First Image Generator](deploy-your-first-image-generator.md) tutorial shows how to apply it to a real Stable Diffusion XL model.

## Before You Start

You'll need:

* Python - we recommend 3.11
* A [fal account](https://fal.ai/dashboard) with Serverless access

> [!NOTE]
>
> **Serverless access is approved per account.** A fal account is free. To deploy your own apps on fal Serverless, the fal team must approve access for your account. Request access on the [Serverless Get Started page](https://fal.ai/dashboard/serverless-get-started). You do not need this approval to call [Model APIs](../../model-apis/overview.md).

## Step 1: Install the CLI

```bash theme={null}
pip install fal
```

## Step 2: Authenticate

Get your API key from the [fal dashboard](https://fal.ai/dashboard) and authenticate:

```bash theme={null}
fal auth login
```

This opens your browser to authenticate with fal. Once complete, your credentials are saved locally.

## Step 3: Create Your First App

Create a file called `hello_world.py` with this simple app:

```python theme={null}
import fal

class MyApp(fal.App):
    @fal.endpoint("/")
    def run(self) -> dict:
        print("Processing Hello World request...")
        return {"message": "Hello, World!"}
```

## Step 4: Validate Locally with `fal run`

**Always validate with `fal run` before you `fal deploy`.** It boots your app on a temporary worker — running `setup()` and your endpoints exactly as production will — so import errors, missing dependencies, and model-loading failures surface here instead of as a production crashloop. Skipping this step is the most common way new apps fail on their first deploy.

```bash theme={null}
fal run hello_world.py::MyApp
```

This starts your app and prints two URLs: a direct HTTP endpoint and a web playground. By default, `fal run` uses `public` auth mode so you can test without an API key.

> [!TIP]
>
> Use `--auth private` if you want to require API key authentication during development. See [Authentication Modes](../../deployment/deploy-to-production.md#authentication-modes) for details.

Once you see `Application startup complete`, test it with curl using the URL from the output:

```bash theme={null}
curl $FAL_RUN_URL -H 'Content-Type: application/json' -d '{}'
```

```json theme={null}
{"message": "Hello, World!"}
```

You can also use the playground URL to test through a browser interface.

## Step 5: Deploy Your App

Once `fal run` works, deploy to create a persistent URL:

```bash theme={null}
fal deploy hello_world.py::MyApp
```

This deploys your app to fal's infrastructure with runners managed automatically based on demand. The output includes your app's endpoint ID and persistent URL.

Open the persistent deployment in the [Apps dashboard](https://fal.ai/dashboard/apps). Follow the [Playground guide](../../model-apis/playground.md#your-own-apps) to test it in your browser. The guide distinguishes this dashboard flow from the temporary `fal run` link.

## Step 6: Call Your Deployed App

Once deployed, you can call your app from any Python or JavaScript application using the fal client SDK.

> [tab] Python
>
> ```bash theme={null}
> pip install fal-client
> ```
>
> ```python theme={null}
> import fal_client
>
> result = fal_client.subscribe("your-username/hello-world", arguments={})
> print(result)
> # {"message": "Hello, World!"}
> ```

> [tab] JavaScript
>
> ```bash theme={null}
> npm install @fal-ai/client
> ```
>
> ```javascript theme={null}
> import { fal } from "@fal-ai/client";
>
> const result = await fal.subscribe("your-username/hello-world", {
>   input: {},
> });
> console.log(result.data);
> // { message: "Hello, World!" }
> ```

Replace `your-username/hello-world` with the endpoint ID shown after deploying. See [Calling Your Endpoints](../calling-your-endpoints.md) for all calling patterns including async queue, streaming, real-time, and webhooks.

## Next Steps

From here, the [Deploy Your First Image Generator](deploy-your-first-image-generator.md) tutorial applies the same workflow to a real Stable Diffusion XL model. To understand how apps are structured and how runners start up and shut down, read [App Lifecycle](../app-lifecycle.md). For all the ways to call your deployed app, including async queue, streaming, real-time, and webhooks, see [Calling Your Endpoints](../calling-your-endpoints.md).
