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

# Test Models and Endpoints

> Test your fal App endpoints programmatically using AppClient, which deploys an ephemeral instance and runs your tests against live infrastructure.

Before [deploying to production](../deployment/deploy-to-production.md), you want to verify that your endpoints produce correct outputs, handle edge cases gracefully, and perform within acceptable latency. The `AppClient` in the fal SDK gives you a way to do this programmatically -- it deploys your app to fal's serverless infrastructure in ephemeral mode, runs your tests against the live endpoints (including GPU execution, `setup()`, and the full request pipeline), and cleans up the deployment when testing is complete.

This means your tests run against the real environment your app will use in production, not a mocked local version. If your model loads correctly in `setup()`, processes inputs through your endpoint, and returns valid outputs, you can be confident the deployed version will behave the same way. You can integrate these tests into your CI pipeline to catch regressions before they reach production.

## Testing a Deployed App in the Browser

After `fal deploy`, open the app in the [Apps dashboard](https://fal.ai/dashboard/apps). Follow [Your Own Apps](../model-apis/playground.md#your-own-apps) for manual endpoint checks. The automated tests below use `AppClient` to create a separate, temporary deployment.

## Testing with AppClient

`AppClient` connects to your app class and exposes its endpoints as callable methods. It handles deployment, connection, and teardown automatically via a context manager.

```python theme={null}
import fal
from pydantic import BaseModel, Field
from fal.toolkit import Image

class ImageModelInput(BaseModel): # ...

class MyApp(fal.App):  # ...
    keep_alive = 300

    def setup(self): # ...

    @fal.endpoint("/")
    def generate_image(self, request: ImageModelInput) -> Image: # ...
```

Now you can write comprehensive tests for this app:

```python theme={null}
def test_myapp():
    with fal.app.AppClient.connect(MyApp) as client:
        result = client.generate_image({"prompt": "A cat holding a sign that says hello world"})
        assert result is not None
        assert hasattr(result, 'url')
```

## Running locally with `fal run --local`

When you want a tighter feedback loop than an ephemeral deployment can give you, `fal run --local` executes your function or app on your own machine instead of provisioning a remote runner. For apps, this starts the FastAPI server on `localhost` so you can hit your endpoints directly:

```bash theme={null}
fal run --local path/to/myfile.py::MyApp
```

The startup logs print the local URL. From another terminal, call your endpoints with any HTTP client:

```bash theme={null}
curl -X POST http://localhost:8080/ \
  -H "Content-Type: application/json" \
  -d '{"prompt": "A cat holding a sign that says hello world"}'
```

This skips the remote machine spin-up entirely, which makes it well suited for quick checks while you're tweaking endpoint code, validating request and response shapes, or stepping through your app with a local debugger.

Because the app runs in your local Python environment, you are responsible for having the right dependencies and hardware available — `setup()` runs on your machine, and there is no GPU unless your machine has one. When you need to verify behavior on the real serverless environment (containers, GPUs, scaling, cold starts), use `AppClient` as shown above instead.
