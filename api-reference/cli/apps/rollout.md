> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../../documentation/quickstart.md) and the [Model APIs overview](../../../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# fal apps rollout

> Restart all runners of a fal app without a redeploy with fal apps rollout. Compare a graceful rollout that drains requests with a forced one.

```bash theme={null}
Usage: fal apps rollout [-h] [--debug] [--pdb] [--cprofile] [--team TEAM] 
                        [--force] [--env ENV] app_name

Rollout application by restarting all active runners.

Positional Arguments:
  app_name     Application name.

Options:
  -h, --help   show this help message and exit
  --team TEAM  The team to use.
  --force      Force rollout by killing runners immediately instead of 
               gracefully stopping them.
  --env ENV    Target environment (defaults to main).

Debug:
  --debug      Show verbose errors.
  --pdb        Start pdb on error.
  --cprofile   Show cProfile report.
```

## When to Use

Use `fal apps rollout` when you need to replace all runners for an application without redeploying:

* **Environment variable changes**: Force runners to pick up updated secrets or environment variables
* **Bad state recovery**: Replace runners that may be in an unhealthy state
* **Memory cleanup**: Force garbage collection and memory cleanup across all runners
* **Configuration updates**: Apply changes that require a fresh runner
* **Machine type changes**: Move existing runners onto a machine type you changed with `fal apps scale` -- see [Changing Machine Types](../../../documentation/deployment/machine-types.md#rolling-out-existing-runners)

## Graceful vs Force Rollout

### Graceful Rollout (Default)

```bash theme={null}
fal apps rollout myapp
```

Brings up replacement runners before draining the existing ones, so requests are not interrupted. This is the recommended approach for most situations.

**How it works:**

1. Identifies all active runners for the application
2. Marks each one for replacement -- they keep serving requests in the meantime
3. Starts replacement runners on the application's current configuration
4. As each new runner finishes starting up and joins the application, one marked runner stops accepting new requests and drains the ones it is still processing

Replacements are started in parallel rather than one at a time, so while the rollout is in progress the application can run close to double its usual runner count -- and is billed for both sets. If your account's GPU limits are already fully consumed, replacements wait until capacity frees up and the marked runners keep serving in the meantime.

### Force Rollout

```bash theme={null}
fal apps rollout myapp --force
```

Immediately kills all active runners without waiting for current requests to complete. Use this when runners are unresponsive or when you need an immediate restart.

**How it works:**

1. Identifies all active runners for the application
2. Immediately terminates all runners
3. New runners are automatically started by the auto-scaling system

> [!WARNING]
>
> Force rollout will terminate in-flight requests, potentially causing errors for active users. Use the graceful rollout (without `--force`) unless absolutely necessary.

## Examples

### Rollout after updating a secret

```bash theme={null}
# Update a secret
fal secrets set MY_API_KEY new_value

# Gracefully rollout to pick up the new secret
fal apps rollout myapp
```

### Rollout in a specific environment

```bash theme={null}
# Update a secret in staging
fal secrets set MY_API_KEY staging_value --env staging

# Rollout the staging environment
fal apps rollout myapp --env staging
```

### Force rollout to recover from frozen runners

```bash theme={null}
# Check runners status
fal apps runners myapp

# Force immediate restart if runners are unresponsive
fal apps rollout myapp --force
```

## Difference from Redeployment

`fal apps rollout` is different from `fal deploy`:

* **`fal apps rollout`**: Replaces existing runners without creating a new revision, so they come up on the app's current configuration. Useful for applying environment or machine-type changes and recovering from bad states.
* **`fal deploy`**: Creates a new revision of your application with updated code and configuration. Use this when you've made code changes.

## See Also

* [fal deploy](https://fal.ai/docs/serverless/cli/deploy.md) - Deploy a new revision of your application
* [fal apps scale](https://fal.ai/docs/serverless/cli/apps/scale.md) - Adjust scaling parameters
* [fal apps runners](https://fal.ai/docs/serverless/cli/apps/runners.md) - View active runners for an application
