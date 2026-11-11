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

# Secrets: Store API Keys and Hugging Face Tokens

> Store Hugging Face tokens, API keys, and database URLs as encrypted fal secrets. Set them in the dashboard, CLI, or SDK and read them with os.getenv().

Most AI applications need credentials to call external services -- Hugging Face tokens for gated models, database URLs, third-party API keys, or [private Docker registry](private-registries.md) credentials. Secrets let you store these values securely on fal instead of hardcoding them in your code or committing them to version control. Once set, secrets are encrypted and their values are never displayed again.

Secrets are injected into your [runners](https://fal.ai/docs/documentation/getting-started/runners-and-caching) as environment variables when they start. You access them via `os.getenv()` just like any other environment variable. Secrets can be scoped to specific [environments](../deployment/manage-environments.md) (e.g., different API keys for staging vs production) and are separate from the [platform environment variables](environment-variables.md) that fal injects automatically.

## Setting Secrets

You can set secrets from the [Dashboard](https://fal.ai/dashboard/secrets), the CLI, or the Python SDK.

### Dashboard

Navigate to [**Dashboard > Secrets**](https://fal.ai/dashboard/secrets) to create, edit, and delete secrets. Select the environment (e.g., main, staging) from the dropdown, then add your key-value pairs. Values are encrypted and never displayed after creation.

### CLI

```bash theme={null}
fal secrets set HF_TOKEN=hf_abc123 DATABASE_URL=postgres://...
```

You can set multiple secrets in a single command. To target a specific environment, use the `--env` flag:

```bash theme={null}
fal secrets set HF_TOKEN=hf_staging_token --env staging
```

### Python SDK

```python theme={null}
from fal.api import SyncServerlessClient

client = SyncServerlessClient()
client.secrets.set("HF_TOKEN", "hf_abc123")
client.secrets.set("HF_TOKEN", "hf_staging_token", environment_name="staging")
```

## Accessing Secrets at Runtime

Secrets are available as standard environment variables inside your app. Use `os.getenv()` to read them in `setup()`, endpoint handlers, or anywhere else in your runner code.

```python theme={null}
import os
import fal
from pydantic import BaseModel

class Input(BaseModel):
    prompt: str

class MyApp(fal.App):
    def setup(self):
        import huggingface_hub
        huggingface_hub.login(token=os.getenv("HF_TOKEN"))
        self.pipe = load_gated_model()

    @fal.endpoint("/")
    def generate(self, input: Input) -> dict:
        result = self.pipe(input.prompt)
        return {"output": result}
```

> [!NOTE]
>
> Secrets are injected when a runner starts. If you update a secret, running runners keep the old value. Only new runners (from a new deploy or scale-up) pick up the updated value. To force all runners to use the new value, redeploy your app with `fal deploy`.

## Scoping Secrets to an App

By default, every secret in a deploy's [environment](../deployment/manage-environments.md) is injected into the app. When an app only needs a couple of them, you can declare an allowlist with the `secrets` class attribute so the runner receives only the secrets you name -- nothing else from your account is exposed. Any account can use the attribute; it just needs a recent fal SDK (see the version note below).

```python theme={null}
import os
import fal
from openai import OpenAI
from pydantic import BaseModel

class Input(BaseModel):
    prompt: str

class MyApp(fal.App):
    # Only OPENAI_API_KEY and HF_TOKEN are injected; all other
    # account secrets are withheld from this app.
    secrets = ["OPENAI_API_KEY", "HF_TOKEN"]

    @fal.endpoint("/")
    def generate(self, input: Input) -> dict:
        client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))
        ...
```

The attribute is a list of secret names, never values -- the values still come from the secrets you set via the Dashboard, CLI, or SDK. Names that don't match an existing secret are ignored. The allowlist applies on top of [per-environment scoping](#secrets-per-environment): a runner only ever sees secrets that both exist in its environment **and** appear in the allowlist.

The attribute has three states. An explicit value (a list or an empty list) always behaves the same way; **only the omitted case depends on how your account is configured**:

| `secrets` value | If your account **exposes** secrets by default | If your account **withholds** secrets by default |
| - | - | - |
| Omitted (the default) | Every secret in the environment, minus any marked not-exposed | **No secrets**, except any marked exposed |
| `["NAME_A", "NAME_B"]` | Only the named secrets | Only the named secrets |
| `[]` (empty list) | No secrets | No secrets |

In other words, the two account modes differ on a single point -- **what an app that says nothing gets**. Expose-by-default (the standard setting) gives such an app every secret in the environment; withhold-by-default gives it none -- in both cases subject to each secret's own exposure setting, described next. Because an explicit `secrets = [...]` (or `[]`) takes priority over the account setting, an app that declares its secrets behaves identically on either kind of account -- so a switch to withhold-by-default never changes what a fully-declared app receives, and only affects apps that omit the attribute.

There is one refinement to the omitted row: each secret also carries its own exposure setting, which takes precedence over the account default. A secret stored with `fal secrets set NAME=VALUE --not-exposed-by-default` is withheld from apps that omit the attribute even on an expose-by-default account, while a secret stored as exposed (which is what a current `fal secrets set` does without the flag) is injected into such apps even on a withhold-by-default account. An explicit allowlist (or `[]`) ignores this setting entirely. In practice this matters most on withhold-by-default accounts: a plain `fal secrets set` marks the secret exposed, so pass `--not-exposed-by-default` when setting secrets (or declare an allowlist in every app) if you rely on the account default to withhold them.

Withhold-by-default is configured by fal on a per-account basis (it isn't self-serve) -- [contact fal](mailto:support@fal.ai) if you'd like your account switched to it. The `secrets` attribute itself works regardless of which mode your account is in.

> [!NOTE]
>
> Per-app secrets require **fal SDK v1.72.3 or newer**. On older SDK versions the `secrets` attribute is ignored and the app falls back to the omitted behavior above (the first row of the table). Check your version with `fal --version` and upgrade with `pip install --upgrade fal`.

> [!WARNING]
>
> On a withhold-by-default account, an app that sets `secrets = []` receives no secrets, and one that omits the attribute receives only secrets individually marked as exposed by default. For any secret that isn't injected, `os.getenv("MY_SECRET")` returns `None` -- declare an allowlist for the secrets each app needs. Contact fal if you're unsure how your account is configured.

## Secrets During Build

For security, secrets are **not** available as environment variables during the image build stage. The built image is cached and may be shared across environments, so injecting secrets into the build would risk leaking them.

Instead, use the `${}` substitution syntax in your `requirements` list. fal replaces `${SECRET_NAME}` with the secret value at build time without exposing it in the cached image.

```python theme={null}
import fal

class MyApp(fal.App):
    requirements = [
        "git+https://${GITHUB_TOKEN}@github.com/myorg/private-package"
    ]
```

This is the only way to use secrets during dependency installation. The substitution happens server-side before pip runs, and the token is not stored in the final image.

Refer to [Private Packages](https://fal.ai/docs/documentation/getting-started/manage-dependencies#private-packages) for private index URLs and for hosted registry credentials. That page also lists the common mistakes.

> [!WARNING]
>
> fal does not fail the build for a name that it cannot find. It keeps the
> placeholder in the string. pip then receives the literal text `${MY_SECRET}`,
> and the install fails with an authentication error. Compare each name with the
> output of `fal secrets list` for the environment that you deploy to. Write `$$`
> for one literal `$` character.

### Docker Build Secrets

If you use a [custom container image](use-custom-container-image.md), you can pass build-time secrets via the `secrets` parameter on `ContainerImage`. These are mounted as Docker build secrets (via `--mount=type=secret`) and are available inside your Dockerfile during the build but not persisted in the final image.

The values accept the same `${SECRET_NAME}` replacement as `requirements`. fal
resolves the placeholder from the secret that you stored.

```python theme={null}
from fal.container import ContainerImage

image = ContainerImage.from_dockerfile_str(
    """
    FROM python:3.11
    RUN --mount=type=secret,id=hf_token pip install --no-cache-dir \
        --extra-index-url https://user:$(cat /run/secrets/hf_token)@pypi.example.com/simple \
        your-private-package
    """,
    secrets={"hf_token": "${HF_TOKEN}"},
)
```

> [!TIP]
>
> Use `"${HF_TOKEN}"` here instead of `os.getenv("HF_TOKEN")`. `os.getenv` reads
> the environment of the machine that runs `fal deploy`. That machine must hold
> the credential, which applies to each developer and to each CI job. If the
> variable is absent, `os.getenv` returns `None` and gives no warning. The
> `${...}` form resolves from the secret stored on fal. The value then stays in
> one place.

This replacement also applies when fal builds your Dockerfile with
`use_isolate=False`, which `requirements` does not. Use it to supply build
credentials in that mode. An image that is already built and pushed to a
registry has no build step. To pull that image, use
[registry credentials](private-registries.md).

## Secrets Per Environment

Secrets can be scoped to specific [environments](../deployment/manage-environments.md), letting you use different credentials for development, staging, and production. When you deploy to an environment, the runner receives the secrets set for that environment.

```bash theme={null}
fal secrets set DATABASE_URL=postgres://prod-db.example.com/app
fal secrets set DATABASE_URL=postgres://staging-db.example.com/app --env staging
```

If a secret is not set for a specific environment, the runner does not fall back to the main environment's value -- the variable is simply not present.

## Managing Secrets

### Listing

List your secrets to see their names, environments, creation dates, and exposure settings. Values are never displayed.

```bash theme={null}
fal secrets list
```

| Name | Env | Created At | Exposed By Default |
| - | - | - | - |
| HF\_TOKEN | main | 2026-01-15 10:30:00 | yes |
| DATABASE\_URL | main | 2026-01-15 10:31:00 | yes |

To list secrets for a specific environment:

```bash theme={null}
fal secrets list --env staging
```

### Removing

Delete a secret to prevent it from being injected into new runners:

```bash theme={null}
fal secrets unset HF_TOKEN
fal secrets unset HF_TOKEN --env staging
```

Running runners are not affected by deletion. The secret is removed from new runners only.
