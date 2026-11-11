> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](overview.md).
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

# Playground

> Test any model with real inputs, see results, and copy working code.

Every model on fal has a Playground where you can try it with real inputs, see outputs, and copy working code in Python, JavaScript, or cURL. Catalog models use the Playground on their model pages. When you [deploy your own app](../serverless.md), you can test its available endpoints from **Testing > Playground** in the app dashboard.

![Playground for Nano Banana 2 on fal.ai](https://mintcdn.com/fal-d8505a2e/8WsQEbl1OYp5vQ4g/images/examples/playground-nano-banana-2.png?fit=max\&auto=format\&n=8WsQEbl1OYp5vQ4g\&q=85\&s=e230ae5495b191fca50bd9ec27194ab4)

The Playground is the fastest way to validate a model before writing any integration code. Once you have a result you like, copy the generated code into your project and you are ready to go. If you want to compare multiple models side by side, use the [Sandbox](sandbox.md) instead. For programmatic access, see [Client Setup](inference/client-setup.md).

## Try It

The best way to understand the Playground is to open one. Pick a model and start generating.

> [card] [Nano Banana 2](https://fal.ai/models/fal-ai/nano-banana-2)
>
> Google's fast image generation and editing

> [card] [Veo 3.1](https://fal.ai/models/fal-ai/veo3.1)
>
> Google DeepMind's latest video model with sound

> [card] [ElevenLabs Music](https://fal.ai/models/fal-ai/elevenlabs/music)
>
> High quality, realistic music generation

## What the Playground Shows

Each model page on fal.ai (for example, [Nano Banana 2](https://fal.ai/models/fal-ai/nano-banana-2)) is organized into tabs. The **Playground** tab lets you fill in inputs and run the model directly in your browser. The **API** tab shows the full input and output schemas with type information, so you know exactly what fields are available and what the response looks like. The page also displays pricing, average latency, and ready-to-copy code examples.

Request logs are visible only to the app owner. Non-owner callers can see their request's status and output, but not its logs. For debugging your own app, see [Logging](../development/logging.md#playground-logs).

## Testing a Model

**1. Find a model**

Browse the [model gallery](https://fal.ai/models) or search for a specific model. Click on it to open its page.

**2. Fill in the inputs**

The Playground form is auto-generated from the model's input schema. Required fields are marked, and optional fields have sensible defaults. For models that accept images, video, or audio, you can upload files directly.

**3. Run the model**

Click **Run** to submit your request. The result appears below the form, typically within a few seconds.

**4. Iterate**

Adjust your inputs and run again to refine the result.

## Copying Code

Use the input-mode selector to switch from **Form** or **JSON** to **Python**, **JavaScript**, or **cURL**. The generated snippet uses your current inputs and the selected endpoint, so you can copy it into your project.

```python Python theme={null}
import fal_client

result = fal_client.subscribe("fal-ai/nano-banana-2", arguments={
    "prompt": "a futuristic cityscape at sunset",
    "aspect_ratio": "16:9"
})
print(result["images"][0]["url"])
```

```javascript JavaScript theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/nano-banana-2", {
  input: {
    prompt: "An action shot of a black lab swimming in an inground suburban swimming pool. The camera is placed meticulously on the water line, dividing the image in half, revealing both the dogs head above water holding a tennis ball in it's mouth, and it's paws paddling underwater."
  },
  onQueueUpdate: (update) => {
    if (update.status === "IN_QUEUE") {
      console.log(`Queue position: ${update.queue_position}`);
    } else if (update.status === "IN_PROGRESS") {
      console.log("Processing...");
    }
  },
});
console.log(result.data);
console.log(result.requestId);
```

```bash cURL theme={null}
curl -X POST "https://queue.fal.run/fal-ai/nano-banana-2" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "a futuristic cityscape at sunset", "aspect_ratio": "16:9"}'
```

The generated code includes all the parameters you configured in the form, so there is no gap between what you tested and what you ship.

## Your Own Apps

For a persistent app created with `fal deploy`, use the app dashboard as the canonical way to open its Playground. This flow works regardless of which links your installed CLI release prints.

If a different account is selected, switch to the personal or team account that owns the app.

**1. Open your deployed app**

In the [Apps dashboard](https://fal.ai/dashboard/apps), select the app.

**2. Choose an environment**

If the app has multiple environments, choose the environment you want to test.

**3. Open the Playground**

Select **Playground** under the **Testing** section of the app sidebar.

**4. Select an endpoint**

Choose the endpoint you want to test from the endpoint selector. You can move between the app's available endpoints without leaving the app.

**5. Run a request**

After you fill in the generated input form, click **Run**.

To inspect the submitted request after it is recorded, open **Requests** in the same app.

The app Playground uses the same Playground authentication and billing behavior as the standalone experience. Opening it from the dashboard does not change the authentication mode or billing rules you configured for the deployment.

Some CLI releases also print a standalone Playground URL after `fal deploy`. That URL remains valid. The dashboard flow keeps you in the app and provides one place to switch between its endpoints.

A Playground URL printed by `fal run` belongs to that temporary run. Use it while `fal run` is active.

To control how your app's inputs render in the Playground (image uploaders, hidden fields, field ordering), see [Handle Inputs and Outputs](../development/handle-inputs-and-outputs.md). For example, naming a field with an `image_url` suffix renders it as an image upload widget, and wrapping a field in `Hidden()` keeps it accessible via API but hides it from the Playground form.

## Playground vs Sandbox

The Playground and the [Sandbox](https://fal.ai/sandbox) serve different purposes. The Playground is for testing a single model with specific inputs and copying code. The Sandbox is for comparing multiple models at once, with features like model sets, cost estimates, search across past generations, and shareable links.

| | Playground | Sandbox |
| - | - | - |
| **Purpose** | Test one model or one app endpoint, copy code | Compare multiple models |
| **Where** | A catalog model page, or **Testing > Playground** for a deployed app | [fal.ai/sandbox](https://fal.ai/sandbox) |
| **Your own apps** | Yes, from the [app dashboard](#your-own-apps) after `fal deploy` | Yes, can be added manually |
| **Code generation** | Python, JS, cURL | Not available |
| **Sharing** | Request links, when sharing is permitted | Shareable links with previews |

## More Models to Try

> [card] [Nano Banana 2 Edit](https://fal.ai/models/fal-ai/nano-banana-2/edit)
>
> Intelligent image editing

> [card] [Kling 3.0 Pro](https://fal.ai/models/fal-ai/kling-video/v2.5-turbo/pro/image-to-video)
>
> Cinematic image-to-video

> [card] [Sora 2](https://fal.ai/models/fal-ai/sora-2/text-to-video)
>
> OpenAI's video model with audio

> [card] [Recraft V4 Pro](https://fal.ai/models/fal-ai/recraft/v4/pro/text-to-image)
>
> Professional design visuals

> [card] [Chatterbox TTS](https://fal.ai/models/fal-ai/chatterbox/text-to-speech)
>
> Natural text-to-speech

> [card] [Browse All](https://fal.ai/explore)
>
> 1,000+ models to explore
