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

# Fastest FLUX Endpoint

> We believe fal has the fastest FLUX endpoint in the planet. If you can find a faster one, we guarantee to beat it within one week. 🤝

> [card] [fal-ai/ flux/schnell](https://fal.ai/models/fal-ai/flux/schnell)
>
> `text-to-image`
>
> FLUX.1 \[schnell] is a 12 billion parameter flow transformer that generates high-quality images from text in 1 to 4 steps, suitable for personal and commercial use.
>
> `optimized`

> [card] [fal-ai/ flux/dev](https://fal.ai/models/fal-ai/flux/dev)
>
> `text-to-image`
>
> FLUX.1 \[dev] is a 12 billion parameter flow transformer that generates high-quality images from text. It is suitable for personal and commercial use.
>
> `flux`

Here is a quick guide on how to use this model from an API in less than 1 minute.

Before we proceed, you need to create an [API key](https://fal.ai/dashboard/keys).

This key secret will be used to authenticate your requests to the fal API.

```js theme={null}
fal.config({
  credentials: "PASTE_YOUR_FAL_KEY_HERE",
});
```

Now you can call our Model API endpoint using the [fal js client](https://fal.ai/docs/model-apis/model-endpoints.md):

```js theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/flux/dev", {
  input: {
    prompt:
      "photo of a rhino dressed suit and tie sitting at a table in a bar with a bar stools, award winning photography, Elke vogelsang",
  },
});
```

> [!NOTE]
>
> **Note:**
>
> #### Image Uploads Should Not Waste GPU Cycles
>
> We upload the output image in a background thread so we don't charge any GPU
> time for time spent on the GPU that is not directly inference.
