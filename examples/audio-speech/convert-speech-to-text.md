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

# Convert Speech to Text Tutorial

> Convert speech to text with the fal API. Call the Whisper or Wizper model with fal.subscribe from JavaScript, and pass the URL of the audio file to transcribe.

## How to Convert Speeches using the fal API

To convert speeches to text using the fal API, you need to send a request to the appropriate endpoint with the desired input parameters. The API uses pre-trained models to convert speeches to text based on the provided audio file. This allows you to convert speeches to text by simply providing the audio file.

Here is an example of how to convert speeches to text using the fal API:

```js theme={null}
import { fal } from "@fal-ai/client";

const result = await fal.subscribe("fal-ai/whisper", {
  input: {
    audio_url: "https://storage.googleapis.com/falserverless/model_tests/whisper/dinner_conversation.mp3"
  },
});
```

## How to select the model to use

fal offers a variety of speech-to-text models. You can select the model that best fits your needs based on the quality and accuracy of the speech-to-text conversion. Here are some of the available models:

* [fal-ai/whisper](https://fal.ai/models/fal-ai/whisper): Whisper is a model for speech transcription and translation.
* [fal-ai/wizper](https://fal.ai/models/fal-ai/wizper): Wizper is Whisper v3 Large — but optimized by our inference wizards. Same WER, double the performance!

To select a model, simply specify the model ID in the subscribe method as shown in the example above. You can find more models and their descriptions in the [Text to Image Models](https://fal.ai/models?categories=text-to-image) page.
