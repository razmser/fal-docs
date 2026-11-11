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

# fal deploy

> Deploy a fal app with fal deploy. Set the app name, auth mode, rolling or recreate strategy, and target environment, and add revision messages and annotations.

> [!TIP]
>
> Before deploying, validate your app with [`fal run`](run.md). It boots your app on a temporary worker — running `setup()` and your endpoints just like production — so import and model-loading errors surface locally instead of as a production crashloop. On a first deploy or after a failed deploy, the CLI reminds you to do this. See [Development vs Production](../../documentation/deployment/deploy-to-production.md#development-vs-production).

> [!NOTE]
>
> After a successful deployment, test the persistent app from the [Apps dashboard](https://fal.ai/dashboard/apps) under **Testing > Playground**. The links printed by `fal deploy` depend on your installed CLI release. Follow the [dashboard Playground flow](../../documentation/model-apis/playground.md#your-own-apps) as the stable entry point.

```bash theme={null}
Usage: fal deploy [-h] [--output {pretty,json}] [--json] [--team TEAM]
                  [--app-name APP_NAME] [--auth AUTH]
                  [--strategy {recreate,rolling}] [--attach | --detach]
                  [--no-scale] [--reset-scale] [--check] [--yes]
                  [--message MESSAGE] [--annotation KEY=VALUE] [--no-cache]
                  [--env ENV]
                  [app_ref]

Deploy a fal application. If no app reference is provided, the command will look for a pyproject.toml file with a [tool.fal.apps] section and deploy the application specified with the provided app name.

Positional Arguments:
  app_ref               Application reference. Either a file path or a file path and a function name separated by '::'. If no reference is provided, the command will look for a pyproject.toml file with a [tool.fal.apps] section and deploy the application specified with the provided app name.
                        File path example: path/to/myfile.py::MyApp
                        App name example: my-app (configure team in pyproject.toml)

Options:
  -h, --help            show this help message and exit
  --team TEAM           The team to use.
  --app-name APP_NAME   Application name to deploy with.
  --auth AUTH           Application authentication mode (private, public, shared).
  --strategy {recreate,rolling}
                        Deployment strategy.
  --attach              Attach to the deployment process. Only applies when --strategy is rolling (the default).
  --detach              Do not attach to the deployment process. Only applies when --strategy is rolling (the default).
  --no-scale            Use the previous deployment of the application for scale settings. This is the default behavior.
  --reset-scale         Use the application code for scale settings.
  --check               Show a pre-deployment summary before deploying. Prompts for confirmation unless --yes is also set.
  --yes                 Skip interactive deploy confirmation prompts. When combined with --check, the summary is still shown.
  --message MESSAGE     Freeform message to attach to this revision (e.g, 'add feature')
  --annotation KEY=VALUE
                        Custom key=value pair to attach to this revision (e.g, GIT_SHA=1234567890). Can be repeated. Value must be a string.
  --no-cache            Do not use the cache for the environment build.
  --env ENV             Target environment (defaults to main). Can also be set via FAL_ENV environment variable.

Output:
  --output {pretty,json}
                        Modify the command output
  --json                Output in JSON format (same as --output json)

Examples:
  fal deploy
  fal deploy path/to/myfile.py
  fal deploy path/to/myfile.py::MyApp
  fal deploy path/to/myfile.py::MyApp --app-name myapp --auth public
  fal deploy path/to/myfile.py::MyApp --check
  fal deploy path/to/myfile.py::MyApp --check --yes
  fal deploy path/to/myfile.py::MyApp --env staging
  fal deploy my-app
  fal deploy my-app --message "a1b2c3d fix cold-start"
  fal deploy my-app --annotation DEPLOYER_ID=foo-123 --annotation GIT_SHA=1234567890
```
