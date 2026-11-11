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
> - [Deploy to Production](deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Deploy a Custom Model to Production

> Deploy your own model with fal deploy to get a persistent endpoint. Choose private, public, or shared auth and roll out new versions with zero downtime.

Deploying to production is the step that turns your local app into a permanent, scalable API endpoint. While [`fal run`](https://fal.ai/docs/reference/cli/run) gives you a temporary URL for testing, `fal deploy` creates a persistent deployment that stays available until you explicitly remove it. Your app gets a stable endpoint ID (e.g., `your-username/my-model`) that callers use with the [fal client SDKs](../model-apis/inference.md) or REST API, automatic [runner scaling](scale-your-application.md), and built-in [reliability](../serverless/reliability/retries.md) through the queue system.

Before deploying, make sure you have [installed the CLI](../development/getting-started/installation.md), written your [fal.App](../development/app-lifecycle.md), and tested it locally with `fal run`. This page walks through the deployment process from the initial command through build, configuration, and what happens behind the scenes. For managing deployments after they are live, see [Manage Deployments](manage-deployments.md).

## Development vs Production

During development, use `fal run` to get a temporary endpoint for testing. It creates an ephemeral deployment that is destroyed when you stop the process. By default, `fal run` uses `public` auth mode so you can test without an API key.

```bash theme={null}
fal run path/to/myapp.py::MyApp
```

When you are ready for production, switch to `fal deploy`. This creates a persistent deployment with a permanent URL. By default, `fal deploy` uses `private` auth mode, requiring an API key to call your app.

```bash theme={null}
fal deploy path/to/myapp.py::MyApp
```

## Deploying Your App

The simplest deployment requires just the path to your app file and class:

```bash theme={null}
fal deploy path/to/myapp.py::MyApp
```

fal builds your app into a container image remotely, pushes it to the registry, and makes it available at the endpoint ID `your-username/app-name`. Callers use this ID with `fal_client.subscribe("your-username/app-name", ...)` or via the REST API at `https://queue.fal.run/your-username/app-name`. You do not need Docker installed locally. The build happens entirely in the cloud.

### Test the Deployment in Playground

After deployment, open the app in the [Apps dashboard](https://fal.ai/dashboard/apps). Follow [Testing Your Own Apps](../model-apis/playground.md#your-own-apps) to test its endpoints. The guide also explains temporary `fal run` links and standalone links from older CLI releases.

### App Name

By default, your app name is derived from the class name (e.g., `TextToImage` becomes `text-to-image`). You can override it in your code or via the CLI. The app name is the second part of your endpoint ID (`your-username/app-name`).

```python theme={null}
class MyApp(fal.App):
    app_name = "my-custom-name"
```

```bash theme={null}
fal deploy my_app.py::MyApp --app-name my-custom-name
```

### Machine Type

Set the hardware your app runs on using the `machine_type` class attribute. For GPU workloads, you can also specify `num_gpus` for multi-GPU configurations. See [Machine Types](machine-types.md) for the full list of available hardware and guidance on choosing the right option.

```python theme={null}
class MyApp(fal.App):
    machine_type = "GPU-A100"
    num_gpus = 1
```

You can also specify multiple machine types to expand your available hardware pool, which reduces queue wait times:

```python theme={null}
class MyApp(fal.App):
    machine_type = ["GPU-A100", "GPU-H100"]
```

Changing `machine_type` in code takes effect on your next `fal deploy`: the new revision's runners come up on the new type and fal drains the old revision's runners as part of the deployment (see [Rollout Strategies](#rollout-strategies) below). Changing the machine type on a live app with `fal apps scale` or from the dashboard behaves differently -- only new runners use the new type, and existing runners keep the old one until you [roll them out](machine-types.md#rolling-out-existing-runners). See [Changing Machine Types](machine-types.md#changing-machine-types).

### Regions

By default, your app can run in any available region. If you need data residency compliance or want to minimize latency for a specific geography, restrict it:

```python theme={null}
class MyApp(fal.App):
    machine_type = "GPU-A100"
    regions = ["us-east"]
```

### Environments

You can deploy to different [environments](manage-environments.md) (e.g., staging, production) to isolate configurations and [secrets](../development/manage-secrets-securely.md):

```bash theme={null}
fal deploy my_app.py::MyApp --env staging
```

### Annotating Deployments

You can attach a freeform message and custom annotations to each revision at deploy time, and use them to identify the revision later in the dashboard or via the [revisions API](../../platform-apis/v1/serverless/apps/revisions.md).

```bash theme={null}
fal deploy my_app.py::MyApp --message "Deploying new version"
```

Annotations are repeatable key=value pairs, so you can add multiple annotations to a single deployment; duplicate keys are rejected. A revision can carry up to 32 annotations, with keys up to 64 characters and 16 KB total; messages are limited to 2048 characters.

```bash theme={null}
fal deploy my_app.py::MyApp --message "Deploying new version" --annotation GIT_SHA=a1b2c3d4 --annotation DEPLOYING_USER_ID=1234567890
```

> [!NOTE]
>
> The keys `message`, `annotations`, and `openapi` are reserved and can't be used as annotation keys. If an annotation value contains spaces, quote the whole pair: `--annotation "reviewer=Jane Doe"`.

## Deploying from CI/CD

Follow [Deploy from CI/CD](../../examples/serverless/deploy-from-ci-cd.md) for a complete GitHub Actions workflow. The workflow tests the app before each deployment. After you enable deployment, same-repository pull requests deploy to staging after approval and pushes to `main` deploy to production. The walkthrough covers noninteractive authentication, deployment opt-in, and safe handling of fork pull requests.

## Previewing a Deployment

Use the `--check` command line option or set `FAL_DEPLOY_CHECK` to `true` to review what will change before your app is actually deployed:

```bash theme={null}
fal deploy path/to/myapp.py::MyApp --check
FAL_DEPLOY_CHECK=true fal deploy path/to/myapp.py::MyApp
```

The deployment check shows the target app and environment, current revision, source object, authentication mode, deployment strategy, build-cache behavior, and effective production configuration changes. If the app has not been deployed before, it shows the effective deployment values that will be used for the first revision.

When using the deployment check feature, you will by default be prompted for confirmation before deploying. In non-interactive environments, pass `--yes` to skip the prompt while still printing the summary:

```bash theme={null}
fal deploy path/to/myapp.py::MyApp --check --yes
FAL_DEPLOY_CHECK=true fal deploy path/to/myapp.py::MyApp --yes
```

> [!NOTE]
>
> Scaling parameters such as `keep_alive`, `min_concurrency`, and `max_concurrency` are inherited from the previous deployment unless you pass `--reset-scale`. The deployment check calls out code values that will not apply without `--reset-scale`.

## Authentication Modes

Authentication controls who can call your app and who pays for compute. You set it with the `--auth` flag or the `app_auth` class attribute.

**Private** is the default for `fal deploy`. Only the account owner and their [team members](../organizations/managing-teams.md) can call the app, and they must include an API key. All compute is billed to the owner.

**Public** is the default for `fal run`. Anyone with the URL can call the app without authentication. All compute is billed to the owner. Use this for open demos or internal tools where you control access at another layer.

**Shared** allows anyone with the URL to call the app, but callers must authenticate with their own API key. Each caller pays for their own usage. This is how all apps in the [Model Gallery](https://fal.ai/models) work. Shared mode requires admin enablement on your account. See [Publishing to the Marketplace](../serverless/publishing-to-marketplace.md) for the full process of making your app available as a marketplace model.

```python theme={null}
class MyApp(fal.App):
    app_auth = "private"
```

## Rollout Strategies

When you redeploy an app that is already running, fal needs to transition from the old revision to the new one. You can choose between two strategies:

**Rolling** (default) spins up a runner on the new revision *before* switching traffic. fal waits for this runner to complete `setup()` and become healthy. Only after the new runner is confirmed ready does fal switch the alias. This ensures zero downtime but takes longer to complete. If the new runner fails to start (e.g., `setup()` crashes), the deployment is aborted and traffic stays on the old revision.

**Recreate** instantly switches the app alias to the new revision. No runners are started proactively. The first request after redeployment triggers a cold start on the new revision. If your app has `min_concurrency > 0`, the scaling system will eventually bring runners up to meet that minimum, but this happens through normal scaling, not as part of the deploy itself.

```bash theme={null}
fal deploy path/to/myapp.py::MyApp --strategy recreate
```

> [!NOTE]
>
> With rolling deployments, you will see a runner spin up during `fal deploy` even if there is no traffic. This is the health verification step. The deploy command stays open until the runner is confirmed healthy or fails.

For more on managing revisions and rolling back to previous versions, see [Rollbacks](rollbacks.md).

## How Builds Work

When you run `fal deploy`, your code is built into a container image remotely. You do not need Docker installed locally.

**1. Upload**

Your app code is sent to fal's build service. For non-container apps, files listed in `app_files` are included. For [container apps](../development/use-custom-container-image.md), files referenced by `COPY`/`ADD` in your Dockerfile are included automatically.

**2. Build**

fal builds your container image in the cloud using [Depot](https://depot.dev), a remote Docker build service.

**3. Cache check**

A hash is computed from your Dockerfile, dependencies, build args, and container files. If an identical image already exists, the build is skipped entirely.

**4. Push**

The built image is stored in fal's container registry.

**5. Ready**

[Runners](runners.md) pull the image when they start up. Image size directly affects pull time and cold start duration.

> [!NOTE]
>
> Since builds happen remotely, your local machine does not need Docker, GPU drivers, or large amounts of disk space. You only need the `fal` CLI and your code.

### Build Caching

fal uses multiple levels of caching to speed up builds. Build hash deduplication checks whether anything changed since the last build -- if not, the previous image is reused with no build at all. Docker layer caching stores layers per-user in the registry, so unchanged layers from previous builds are reused and only modified layers are rebuilt.

To force a full rebuild bypassing all caching:

```bash theme={null}
fal deploy my_app.py --no-cache
```

For tips on structuring your Dockerfile for faster builds, see [Optimize Container Images](../serverless/optimizations/optimize-container-images.md).

## What's Next

> [card] [Manage Deployments](manage-deployments.md)
>
> List, update, and delete your deployed apps

> [card] [Rollbacks](rollbacks.md)
>
> Roll back to previous revisions or roll out new ones gracefully

> [card] [Scale Your Application](scale-your-application.md)
>
> Configure scaling parameters for production traffic

> [card] [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md)
>
> Reduce image size and startup time
