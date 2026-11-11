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

# Deploy from CI/CD

> Test a private CPU app with pytest, deploy trusted pull requests to staging, and deploy main to production with GitHub Actions.

> [card] [View on GitHub](https://github.com/fal-ai-community/fal-serverless-examples/tree/main/01-environments-containers/deploy-from-ci-cd)
>
> Full source for this example in fal-serverless-examples.

This example connects local endpoint tests to a fal deployment with GitHub Actions.
The app runs on a CPU and returns a greeting. It needs no model weights or GPU.

When you finish, each matching pull request runs local tests. After approval, a same-repository pull request deploys `ci-cd-demo` to fal `staging`. A merge to `main` deploys it to fal `main`, which this example uses for production.

The workflow tests each matching pull request without credentials. Deployment stays off until you set a repository variable, `FAL_CI_CD_ENABLED`, to `true`.

| GitHub event | Tests | Deployment after tests pass and deployment is enabled |
| - | - | - |
| Pull request from the same repository | Local pytest tests | fal `staging`, GitHub environment `staging` |
| Pull request from a fork | Local pytest tests | None |
| Pull request from Dependabot | Local pytest tests | None |
| Push to `main` | Local pytest tests | fal `main`, GitHub environment `production` |
| Manual `workflow_dispatch` | Local pytest tests | fal `staging`, GitHub environment `staging` |

## Running the Example

1. Clone the examples repository:

```bash theme={null}
git clone https://github.com/fal-ai-community/fal-serverless-examples.git
cd fal-serverless-examples/01-environments-containers/deploy-from-ci-cd
```

2. Create a Python 3.11 environment:

```bash theme={null}
python3.11 -m venv .venv
source .venv/bin/activate
```

3. Install the pinned SDK and test runner:

```bash theme={null}
python -m pip install -r requirements.txt pytest==9.1.1
```

The example's `requirements.txt` contains the SDK pin:

```text requirements.txt theme={null}
fal==1.83.0
```

4. Run the local endpoint tests:

```bash theme={null}
python -m pytest -q test_app.py
```

The output ends with `3 passed`.
These tests do not need a fal key or create remote infrastructure.
The deployment workflow runs the same command before it can deploy.

## Defining the App

The request contains a name. The response contains a greeting.
The `CICDDemo` class exposes one endpoint at `/` and requires authentication for deployed requests.

```python app.py theme={null}
"""A tiny CPU app deployed by GitHub Actions after local tests pass."""

import fal
from pydantic import BaseModel, Field


class Input(BaseModel):
    name: str = Field(description="Who to greet.", examples=["fal"])


class Output(BaseModel):
    message: str = Field(description="The greeting.")


class CICDDemo(fal.App, keep_alive=60):
    machine_type = "S"
    app_auth = "private"
    requirements = ["fal==1.83.0"]

    @fal.endpoint("/")
    def greet(self, request: Input) -> Output:
        return Output(message=f"Hello, {request.name}!")
```

`machine_type = "S"` selects a CPU machine. `app_auth = "private"` requires a fal key for each request. `keep_alive=60` keeps a runner warm for 60 seconds after the last request.
The SDK pin matches `requirements.txt` so the local and remote environments use the same version.
The workflow's `--app-name ci-cd-demo` flag gives the app the same name in each fal environment.

## Testing Before Deployment

These tests call the endpoint method directly. They check a greeting and reject missing or non-string names.
`_allow_init=True` opts into local construction for this stateless unit test.
The SDK blocks normal construction outside a fal runner.
The file loader selects this example's `app.py` explicitly, avoiding other examples with the same module name.
In your own repository, `from app import CICDDemo, Input` works instead.

```python test_app.py theme={null}
"""Local endpoint and input-contract tests without credentials or remote compute."""

import importlib.util
from pathlib import Path

import pytest
from pydantic import ValidationError

_spec = importlib.util.spec_from_file_location(
    "ci_cd_demo_app", Path(__file__).parent / "app.py"
)
assert _spec is not None and _spec.loader is not None
app_module = importlib.util.module_from_spec(_spec)
_spec.loader.exec_module(app_module)


def test_greet():
    app = app_module.CICDDemo(_allow_init=True)
    response = app.greet(app_module.Input(name="Ada Lovelace"))
    assert response.model_dump() == {"message": "Hello, Ada Lovelace!"}


def test_name_is_required():
    with pytest.raises(ValidationError):
        app_module.Input.model_validate({})


def test_name_must_be_a_string():
    with pytest.raises(ValidationError):
        app_module.Input.model_validate({"name": 123})
```

These tests do not verify container builds, remote startup, or authenticated requests.
See [Test Models and Endpoints](../../documentation/development/test-models-and-endpoints.md) for local server checks and live `AppClient` tests.
`AppClient` creates a remote deployment, so this credential-free job does not use it.

## Preparing GitHub and fal

1. Copy the `01-environments-containers/deploy-from-ci-cd/` folder into your GitHub repository at the same path. You add the workflow file in the next section.
   If you put the app at a different path, change both `paths` filters and `defaults.run.working-directory` in the workflow to match.
2. Create a fal `staging` environment with the [Manage Environments](../../documentation/deployment/manage-environments.md) guide.
3. Create a fal key with the `DEPLOY` preset. Follow [Key-Based Authentication](../../documentation/model-apis/authentication/key-based.md#permissions) for key creation and permissions.
4. In GitHub, open **Settings > Environments** and create two environments named `staging` and `production`.
5. In each GitHub environment, add an environment secret named `FAL_DEPLOY_KEY` with the fal key as its value. Do not store it as a repository secret.
6. In the `production` environment, set **Deployment branches and tags** to allow only the `main` branch.
7. In both environments, add required reviewers before you do step 8. Reviewers on `staging` are the only gate for pull request deployments.
8. When you are ready to deploy, open **Settings > Secrets and variables > Actions > Variables** and add a repository variable named `FAL_CI_CD_ENABLED` with the exact value `true`.

Until you set `FAL_CI_CD_ENABLED` to `true`, the workflow runs tests and shows the `Deploy app` job as skipped. This is expected.
On private repositories, [GitHub plan limits](https://docs.github.com/en/actions/reference/workflows-and-actions/deployments-and-environments) apply. Environment secrets need GitHub Pro, Team, or Enterprise. Required reviewers need GitHub Enterprise.
If your plan cannot provide these gates, do not set `FAL_CI_CD_ENABLED`. Do not use a repository secret instead.

GitHub environments control the workflow's approvals and secrets. They are separate from fal environments.
The GitHub environment `production` targets fal's `main` environment, not a fal environment named `production`.
The workflow does not configure approval policies for you.
Every job can read a repository secret. A pull request that edits the workflow can then remove the `environment` block and skip its approvals. For this reason, the key goes in environment secrets only.

> [!WARNING]
>
> **Key scope:** the `DEPLOY` preset can change apps, secrets, and environments. The key is not limited to one fal environment.
>
> **Same-repository pull requests:** code in these pull requests can use the key during deployment. Treat that code and its authors as trusted.
>
> **Staging can reach production:** a pull request author can edit the workflow to deploy through the `staging` GitHub environment with `--env main`. The `production` branch rule does not apply to that path.
>
> * Review workflow changes in the pull request before you approve a staging deployment.
> * If staging deployments must never reach production, deploy staging to a separate fal team with its own key.
>
> **Forks and Dependabot:** fork pull requests only run tests and never receive the key. GitHub withholds Actions secrets from Dependabot pull requests, so the workflow skips deployment for them.
>
> Never change this workflow to use `pull_request_target` to deploy pull request code.

The workflow exposes the secret as `FAL_KEY` only in the deploy step. The CLI uses it for noninteractive authentication.
Do not run `fal auth login` in CI.

## Adding the Workflow

Place this file at `.github/workflows/deploy.yml` in the repository root.
GitHub does not discover workflows inside the example folder.

```yaml .github/workflows/deploy.yml theme={null}
name: deploy-ci-cd-demo

on:
  pull_request:
    paths:
      - "01-environments-containers/deploy-from-ci-cd/**"
      - ".github/workflows/deploy.yml"
  push:
    branches: [main]
    paths:
      - "01-environments-containers/deploy-from-ci-cd/**"
      - ".github/workflows/deploy.yml"
  workflow_dispatch:

permissions:
  contents: read

defaults:
  run:
    working-directory: 01-environments-containers/deploy-from-ci-cd

jobs:
  test:
    name: Test app
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - uses: actions/checkout@08c6903cd8c0fde910a37f88322edcfb5dd907a8 # v5.0.0
        with:
          persist-credentials: false
      - uses: actions/setup-python@e797f83bcb11b83ae66e0230d6156d7c80228e7c # v6.0.0
        with:
          python-version: "3.11"
      - name: Install test dependencies
        run: python -m pip install -r requirements.txt pytest==9.1.1
      - name: Run local tests
        run: python -m pytest -q test_app.py

  deploy:
    name: Deploy app
    needs: test
    if: >-
      ${{ vars.FAL_CI_CD_ENABLED == 'true' &&
          ((github.event_name == 'pull_request' &&
            github.event.pull_request.head.repo.full_name == github.repository &&
            github.actor != 'dependabot[bot]') ||
           (github.event_name == 'push' && github.ref == 'refs/heads/main') ||
           github.event_name == 'workflow_dispatch') }}
    runs-on: ubuntu-latest
    timeout-minutes: 20
    environment:
      name: ${{ github.event_name == 'push' && 'production' || 'staging' }}
    concurrency:
      group: ci-cd-demo-${{ github.event_name == 'push' && 'main' || 'staging' }}
      cancel-in-progress: false
    env:
      TARGET_ENV: ${{ github.event_name == 'push' && 'main' || 'staging' }}
    steps:
      - uses: actions/checkout@08c6903cd8c0fde910a37f88322edcfb5dd907a8 # v5.0.0
        with:
          persist-credentials: false
      - uses: actions/setup-python@e797f83bcb11b83ae66e0230d6156d7c80228e7c # v6.0.0
        with:
          python-version: "3.11"
      - name: Install deployment dependencies
        run: python -m pip install -r requirements.txt
      - name: Deploy tested app
        env:
          FAL_KEY: ${{ secrets.FAL_DEPLOY_KEY }}
        run: |
          if [ -z "$FAL_KEY" ]; then
            echo "::error::FAL_DEPLOY_KEY is not configured"
            exit 1
          fi
          fal deploy app.py::CICDDemo --app-name ci-cd-demo \
            --env "$TARGET_ENV" --strategy rolling --reset-scale --attach --yes
```

The table at the top of this page describes the `deploy` job's `if` condition.
The `deploy` job depends on the `test` job. A test failure prevents deployment.
The `fal` CLI comes from the pinned SDK in `requirements.txt`. Each `run` step runs in the example folder because of `defaults.run.working-directory`.
Both jobs check out the event's revision. For pull requests, this is GitHub's merge revision.
The workflow pins both actions to commit SHAs and disables persistent checkout credentials.

The deploy command selects the environment explicitly. `--strategy rolling` selects a rolling deployment.
`--reset-scale` applies the scaling settings in `app.py`, such as `keep_alive`, on every deploy. Without it, a redeploy keeps the previous revision's scaling. With it, scaling changes made in the dashboard are overwritten by the next deploy.
`--attach` keeps the CLI attached to the deployment. `--yes` skips interactive confirmation.
See [Deploy to Production](../../documentation/deployment/deploy-to-production.md#rollout-strategies) for rollout behavior.

The deployment concurrency group separates `staging` from `main`. Deployments to the same target cannot run simultaneously.
`cancel-in-progress: false` lets an active deployment finish when another run arrives.
GitHub concurrency does not guarantee deployment order or preserve every pending run.

## Using the Pipeline

1. Open a pull request that changes the example folder or workflow.
2. In GitHub Actions, confirm that `Test app` passes. For a same-repository pull request, `Deploy app` then waits for approval.
3. Approve the deployment if the pull request is trusted.
4. After deployment, send a request to the staging app. Staging URLs end in `--staging`. Replace `your-user` with your fal username or team name.
   Set `FAL_KEY` to a separate key with the `API` preset from the same account or team. Do not use the `DEPLOY` key for requests.

```bash theme={null}
curl -X POST https://fal.run/your-user/ci-cd-demo--staging \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name": "fal"}'
```

The response is `{"message":"Hello, fal!"}`. You can also see the app in the [Apps dashboard](https://fal.ai/dashboard/apps).
For more checks, see [Test Models and Endpoints](../../documentation/development/test-models-and-endpoints.md).

5. Merge the reviewed change into `main` to trigger the production deployment. The production URL has no suffix: `https://fal.run/your-user/ci-cd-demo`.

Staging is shared, not a separate preview for each pull request.
The next successful staging deployment replaces the code from the previous one.
A merge deploys the tested `main` revision independently. It does not promote the staging app.

To make `Test app` a required check in branch protection, remove the `paths` filter under `pull_request`. Otherwise, pull requests that do not touch these paths wait for a check that never runs.
After you remove it, every same-repository pull request requests a staging deployment.

For a manual staging run, select **Actions > deploy-ci-cd-demo > Run workflow** in GitHub.
The workflow must exist on the default branch for this control to appear.
Manual runs always target staging, even when you select `main` as the source branch.
They still require passing tests, deployment opt-in, a configured key, and any environment approvals.

## Rolling Back a Deployment

Use [Rollbacks and Revisions](../../documentation/deployment/rollbacks.md) to inspect revisions and select a previous version.
For this example's production app, the commands target `main` explicitly:

```bash theme={null}
fal apps list-rev ci-cd-demo --env main
fal apps set-rev ci-cd-demo REVISION_ID --env main --strategy rolling
```

Replace `REVISION_ID` with the revision you choose from the list.
A rollback changes the live app, not the repository. Revert or fix the source before the next automated deployment.

## Other CI Providers

The same `FAL_KEY` environment variable and CLI steps work in GitLab CI and CircleCI.
Store the deployment key in the provider's secret store. Run the local tests before the deploy command.
Keep the same trusted-code restrictions and explicit `--env staging` or `--env main` selection.
