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

# Private Docker Registries

> How to authenticate with private registries like Docker Hub, Google Artifact Registry, AWS ECR, and Azure Container Registry.

When your base image or referenced container image is hosted on a private registry, you need to provide authentication credentials so fal can pull it. This page covers the setup for Docker Hub, Google Artifact Registry, Amazon ECR, and Azure Container Registry, including how to manage tokens securely using fal's [secrets management](manage-secrets-securely.md).

These credentials are passed via the `registries` parameter on [ContainerImage](use-custom-container-image.md), or under the app's `image.registries` configuration in [`pyproject.toml`](../../api-reference/python-sdk/pyproject-toml.md#container-image). In `pyproject.toml`, this works with both `dockerfile` and `image`, and uses the same shape: registry host to `username` and `password`. Each registry uses a different authentication method, so follow the section below that matches your registry provider.

A password written as `$SECRET_NAME` references the fal secret of that name. If your actual password starts with a `$`, escape it as `$$` (for example, write `$$abc` for the literal password `$abc`).

## Dockerhub

```python theme={null}
class MyModel(fal.App):
    image = fal.container.ContainerImage.from_dockerfile_str(
        "FROM myuser/image:tag",
        registries={
            "https://index.docker.io/v1/": {
                "username": "myuser",
                "password": "$DOCKERHUB_TOKEN",  # Use `fal secrets set` to create this secret
            },
        },
    )
```

The same Docker Hub credentials can be configured in `pyproject.toml`:

```toml theme={null}
[tool.fal.apps.my-server]
auth = "private"
machine_type = "GPU-A100"
exposed_port = 8000

[tool.fal.apps.my-server.image]
image = "myuser/image:tag"

[tool.fal.apps.my-server.image.registries."https://index.docker.io/v1/"]
username = "myuser"
password = "$DOCKERHUB_TOKEN"
```

## Google Artifact Registry

We recommend using a service account and setting a base64-encoded version of the key as a fal secret.

**1. Create a JSON key for a service account.**

Download the key file to your local machine.

**2. Encode the key in base64.**

```shell theme={null}
cat key.json | base64
```

**3. Set the result as a fal secret.**

```shell theme={null}
fal secrets set GOOGLE_AR_JSON_BASE64="<base64-value>"
```

**4. Use the secret in your code.**

```python theme={null}
class MyModel(fal.App):
    image = fal.container.ContainerImage.from_dockerfile_str(
        "FROM us-central1-docker.pkg.dev/my-project/my-repo/image:tag",
        registries={
            "us-central1-docker.pkg.dev": {
                "username": "_json_key_base64",
                "password": "$GOOGLE_AR_JSON_BASE64",
            },
        },
    )
```

The same service account credentials can be configured in `pyproject.toml`:

```toml theme={null}
[tool.fal.apps.my-server]
auth = "private"
machine_type = "GPU-A100"
exposed_port = 8000

[tool.fal.apps.my-server.image]
image = "us-central1-docker.pkg.dev/my-project/my-repo/image:tag"

[tool.fal.apps.my-server.image.registries."us-central1-docker.pkg.dev"]
username = "_json_key_base64"
password = "$GOOGLE_AR_JSON_BASE64"
```

## Amazon Elastic Container Registry (ECR)

ECR tokens expire every 12 hours. It's best to auto-generate one on the fly using `boto3` and your AWS credentials.

```python theme={null}
import os
import boto3
import base64

def get_ecr_token(region: str) -> str:
    ecr_client = boto3.client(
        'ecr',
        aws_access_key_id=os.environ["AWS_ACCESS_KEY_ID"],
        aws_secret_access_key=os.environ["AWS_SECRET_ACCESS_KEY"],
        region_name=region
    )
    response = ecr_client.get_authorization_token()
    auth_data = response['authorizationData'][0]
    token = auth_data['authorizationToken']
    # Decode from base64, format is "AWS:password"
    decoded_token = base64.b64decode(token).decode('utf-8')
    _, password = decoded_token.split(':', 1)
    return password

class MyModel(fal.App):
    image = fal.container.ContainerImage.from_dockerfile_str(
        "FROM 123456789012.dkr.ecr.us-east-1.amazonaws.com/image:tag",
        registries={
            "123456789012.dkr.ecr.us-east-1.amazonaws.com": {
                "username": "AWS",
                "password": get_ecr_token("us-east-1"),
            },
        },
    )
```

When using `pyproject.toml`, generate the ECR password outside the config and store it as a fal secret. For example, with an authenticated AWS CLI:

```shell theme={null}
fal secrets set AWS_ECR_PASSWORD="$(aws ecr get-login-password --region us-east-1)"
```

Refresh this secret when the ECR password expires:

```toml theme={null}
[tool.fal.apps.my-server]
auth = "private"
machine_type = "GPU-A100"
exposed_port = 8000

[tool.fal.apps.my-server.image]
image = "123456789012.dkr.ecr.us-east-1.amazonaws.com/image:tag"

[tool.fal.apps.my-server.image.registries."123456789012.dkr.ecr.us-east-1.amazonaws.com"]
username = "AWS"
password = "$AWS_ECR_PASSWORD"
```

## Azure Container Registry (ACR)

We recommend using a service principal with the `AcrPull` role. Unlike ECR there is no short-lived token to refresh. Note that service principal passwords created by `az ad sp create-for-rbac` are valid for 1 year by default (pass `--years` to adjust), so update the fal secret when you rotate the credential.

**1. Create a service principal with pull access to your registry.**

```shell theme={null}
az ad sp create-for-rbac \
  --name fal-acr-pull \
  --role AcrPull \
  --scopes "$(az acr show --name myregistry --query id --output tsv)"
```

Note the `appId` and `password` from the output.

**2. Set the password as a fal secret.**

```shell theme={null}
fal secrets set 'AZURE_ACR_PASSWORD=<service-principal-password>'
```

Quote the value: service principal passwords often contain characters your shell would otherwise interpret.

**3. Use the secret in your code.**

```python theme={null}
class MyModel(fal.App):
    image = fal.container.ContainerImage.from_dockerfile_str(
        "FROM myregistry.azurecr.io/image:tag",
        registries={
            "myregistry.azurecr.io": {
                "username": "<service-principal-app-id>",
                "password": "$AZURE_ACR_PASSWORD",
            },
        },
    )
```

A [repository-scoped ACR token](https://learn.microsoft.com/en-us/azure/container-registry/container-registry-token-based-repository-permissions) or the registry's admin user also work as the username/password pair. Avoid pasting the output of `az acr login --expose-token`, since those Entra ID access tokens expire after a few hours.

The same service principal credentials can be configured in `pyproject.toml`:

```toml theme={null}
[tool.fal.apps.my-server]
auth = "private"
machine_type = "GPU-A100"
exposed_port = 8000

[tool.fal.apps.my-server.image]
image = "myregistry.azurecr.io/image:tag"

[tool.fal.apps.my-server.image.registries."myregistry.azurecr.io"]
username = "<service-principal-app-id>"
password = "$AZURE_ACR_PASSWORD"
```
