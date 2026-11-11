> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Define Your Endpoints

> How to define, structure, and configure the API endpoints your fal App exposes to callers.

Every [fal App](https://fal.ai/docs/documentation/getting-started/apps-and-execution) exposes one or more endpoints that callers interact with through the fal client SDKs or REST API. An endpoint is a Python method decorated with `@fal.endpoint("/path")` that receives input, runs your model or logic, and returns output. The simplest app has a single root endpoint at `/`, but you can define as many endpoints as you need at different paths, including specialized [streaming](streaming.md) and [real-time](realtime.md) endpoints for progressive and bidirectional communication.

This section covers everything involved in building endpoints, from defining input and output schemas to handling files, cancellations, and observability. If you have already set up your [application environment](app-setup.md) and [loaded your model](download-model-weights-and-files.md), this is where you define how users interact with it. Each page below focuses on a specific aspect of endpoint development. Start with a basic endpoint, then layer on the capabilities you need.

## A Basic Endpoint

At minimum, an endpoint is a method on your `fal.App` class decorated with `@fal.endpoint`. It receives typed input and returns typed output. The types you use determine both the API schema and how the [Playground](../model-apis/playground.md) renders the input form and output preview.

```python theme={null}
import fal
from pydantic import BaseModel

class Input(BaseModel):
    prompt: str

class Output(BaseModel):
    text: str

class MyApp(fal.App):
    machine_type = "GPU-A100"

    def setup(self):
        self.model = load_model()

    @fal.endpoint("/")
    def generate(self, input: Input) -> Output:
        result = self.model(input.prompt)
        return Output(text=result)
```

You can define multiple endpoints on the same app at different paths. Callers reach non-root endpoints by appending the path to the endpoint URL (e.g., `https://fal.run/your-username/your-app/enhance`).

```python theme={null}
class MyApp(fal.App):
    @fal.endpoint("/")
    def generate(self, input: GenerateInput) -> GenerateOutput:
        ...

    @fal.endpoint("/enhance")
    def enhance(self, input: EnhanceInput) -> EnhanceOutput:
        ...
```

## What's in This Section

The pages below cover each aspect of endpoint development. [Handle Inputs and Outputs](handle-inputs-and-outputs.md) explains how to define Pydantic schemas that control both API validation and how the Playground renders your input form and output preview. [Working with Files](working-with-files.md) covers downloading input files and uploading generated outputs to the CDN at runtime.

For progressive output, [Streaming](streaming.md) shows how to send partial results to clients via Server-Sent Events. For bidirectional communication with persistent connections, see [Realtime](realtime.md). [Testing](test-models-and-endpoints.md) covers automated endpoint testing and CI setup, [Health Checks](add-health-check-endpoint.md) let fal detect and replace unhealthy runners, and [Cancellations](handle-cancellations.md) show how to free GPU resources when callers cancel in-flight requests.
