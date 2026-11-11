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

# fal Runtime

> How to use fal's managed Python runtime to run your models.

The fal runtime is the recommended way to define your app's environment. Instead of writing a Dockerfile, you list your pip dependencies in the `requirements` attribute and fal builds an optimized, cached environment for you. This approach handles CUDA setup, PyTorch index URLs, and environment isolation automatically based on your chosen [machine type](../deployment/machine-types.md).

If you need system-level packages, a specific base image, or a non-Python runtime, use a [custom container](use-custom-container-image.md) instead. If you need to include local Python packages, local modules, or external repositories, see [Import Code](import-code.md). For an overview of when to use which approach, see [Environment and Runtime](container-setup.md).

## Defining Requirements

The `requirements` attribute in your `fal.App` class is where you specify the Python packages your model needs. fal ensures these are installed in the [runner's](../deployment/runners.md) environment before [setup()](app-lifecycle.md#runner-startup) is called.

```python theme={null}
import fal

class MyModel(fal.App):
    machine_type = "GPU-A100"
    
    requirements = [
        "torch==2.4.0",
        "transformers",
        "diffusers",
        "accelerate"
    ]
    
    def setup(self):
        import torch
        ...
```

Pin your package versions (e.g., `torch==2.4.0`) to ensure reproducible builds. Use the `requirements` attribute instead of running `pip install` inside `setup()`, since packages in `requirements` are installed during the container build and cached across deploys. Only include the packages your app actually needs to keep startup times short.

When you deploy, fal creates an isolated environment based on your `machine_type`, installs your listed packages, and caches the resulting container image. On subsequent deploys, unchanged requirements are served from cache. Your `setup()` method then runs on the provisioned runner to load the model into memory.

## Using Prebuilt Wheels

You can install packages directly from wheel URLs. This is useful for custom-built packages or packages not available on PyPI.

### Direct URL

Provide the full URL to a wheel file:

```python theme={null}
requirements = [
    "https://your-storage.example.com/wheels/mypackage-1.0.0-cp311-cp311-linux_x86_64.whl",
]
```

### Package @ URL (PEP 440)

Use the `package@url` syntax to give the package a name for dependency resolution:

```python theme={null}
requirements = [
    "mypackage@https://your-storage.example.com/wheels/mypackage-1.0.0-cp311-cp311-linux_x86_64.whl",
]
```

This syntax is recommended when other packages depend on `mypackage`, as pip can properly track the dependency.

## Alternative Package Indexes

Use `--extra-index-url` or `--find-links` to install packages from alternative sources.

### Extra Index URL

Install packages from an additional PyPI-compatible index:

```python theme={null}
requirements = [
    "torch==2.5.0",
    "--extra-index-url",
    "https://download.pytorch.org/whl/cu124",
]
```

> [!WARNING]
>
> The `--extra-index-url` flag must appear **before** any packages that need it. Place index flags at the beginning or directly before the relevant packages.

### Find Links

Use `--find-links` to search for packages in a directory or URL containing wheel files:

```python theme={null}
requirements = [
    "mypackage",
    "--find-links",
    "https://github.com/your-org/releases/download/v1.0.0/",
]
```

### Multiple Indexes

Combine multiple index sources when needed:

```python theme={null}
requirements = [
    "--extra-index-url",
    "https://download.pytorch.org/whl/cu124",
    "--extra-index-url",
    "https://your-company.example.com/simple",
    "torch==2.5.0",
    "your-internal-package==1.0.0",
]
```

## Private Packages

fal gives the `requirements` list to pip without changes. Any pip syntax for a
private source works. Only the way you supply the credentials is different.

### Credentials at Build Time

To authenticate a private install, write `${SECRET_NAME}` in the requirement
string. fal replaces the placeholder with the value of the secret. This
replacement runs on the fal servers during your deploy, before the build
starts.

```bash theme={null}
fal secrets set PYPI_TOKEN=your-token-here
```

```python theme={null}
import fal

class MyApp(fal.App):
    requirements = [
        "--extra-index-url",
        "https://user:${PYPI_TOKEN}@pypi.your-company.com/simple",
        "your-private-package==1.0.0",
    ]
```

The spelling looks like a shell variable, but this is not an environment
variable. fal does not start a shell and does not read the environment. fal
changes the string itself. pip then receives the complete URL as a normal
argument.

This is the only way to authenticate a dependency install. During the build,
fal does not put your secrets in the environment. Your app code has not started
at that point, and `os.getenv()` cannot help you. The install is already
complete before the app starts.

fal withholds the secrets because it caches each build artifact and uses it
again for later deploys. A build that can read a secret can write that secret
into a cached layer. fal would then give that layer to a different build.
Replacement resolves only the strings that you mark, so the build receives no
other secret.

> [!NOTE]
>
> A new secret value produces a different requirement string. This changes the
> cache key of the environment. The first deploy after you rotate a secret builds
> again instead of using the cache.

> [!WARNING]
>
> Replacement does not apply to `requirements` in
> [Direct Server Mode](migrate-external-docker-server.md)
> (`use_isolate=False`). In this mode fal does not build a Python environment for
> you. fal keeps the placeholder in the string and does not resolve it. Give the
> credentials to `ContainerImage(secrets={...})` instead, which does support
> `${...}`. Refer to
> [Docker Build Secrets](manage-secrets-securely.md#docker-build-secrets).

### Private Git Repositories

Install directly from a private GitHub repository:

```python theme={null}
requirements = [
    "git+https://${GITHUB_TOKEN}@github.com/your-org/private-repo.git",
]
```

Pin to a specific commit or tag for reproducibility:

```python theme={null}
requirements = [
    "git+https://${GITHUB_TOKEN}@github.com/your-org/private-repo.git@v1.0.0",
    "git+https://${GITHUB_TOKEN}@github.com/your-org/private-repo.git@abc123def",
]
```

### Private Package Index

Install from a private index server with authentication:

```python theme={null}
requirements = [
    "--extra-index-url",
    "https://${INDEX_USER}:${INDEX_TOKEN}@pypi.your-company.com/simple",
    "your-private-package==1.0.0",
]
```

> [!NOTE]
>
> `--extra-index-url` adds your index to PyPI. pip then installs the highest
> version that it finds in **either** index. A public package with the same name
> as your private package can replace it. If your server supplies all the
> packages that you need, use `--index-url`. pip then reads only that index.

#### Hosted Registries

Most managed registries use a fixed username and a token as the password.

| Registry | Username | Password |
| - | - | - |
| Google Artifact Registry | `_json_key_base64` | base64 of a service account key JSON |
| AWS CodeArtifact | `aws` | token from `aws codeartifact get-authorization-token` |
| Azure Artifacts | any non-empty string | a personal access token |
| JFrog Artifactory | your username | an API key or identity token |

For Google Artifact Registry, create a service account key. Encode the key and
store it in one step. This command prevents two problems. The `base64` command
adds a newline at the end of its output. The encoded value also contains `+`,
`/`, and `=` characters, which a URL does not permit.

```bash theme={null}
fal secrets set GCP_ARTIFACT_KEY=$(python3 -c "
import base64, urllib.parse
data = open('key.json','rb').read()
print(urllib.parse.quote(base64.b64encode(data).decode(), safe=''))
")
```

```python theme={null}
requirements = [
    "--extra-index-url",
    "https://_json_key_base64:${GCP_ARTIFACT_KEY}@us-python.pkg.dev/your-project/your-repo/simple",
    "your-private-package==1.0.0",
]
```

pip decodes the URL encoding before it sends the authentication header. The
registry receives the original base64 value.

> [!WARNING]
>
> Tokens from AWS CodeArtifact and Azure Artifacts expire. A CodeArtifact token
> is valid for 12 hours at most. fal does not refresh a stored secret, so a build
> that was successful yesterday can fail today with a 401 error. Use a long-lived
> credential if the registry supplies one. If it does not, set the secret again
> in each deploy.

### Pre-signed URLs

For one or two wheels, it is simpler to omit the index. Generate a pre-signed
URL from your private storage and install the wheel directly. This method needs
no index flag and no registry credentials.

```python theme={null}
requirements = [
    "https://your-bucket.s3.amazonaws.com/wheels/mypackage-1.0.0.whl?AWSAccessKeyId=...&Signature=...&Expires=...",
]
```

A pre-signed URL expires. Generate the URL in each deploy. Do not commit a
long-lived URL to your repository.

### Troubleshooting

* **The log shows a literal `${MY_SECRET}`, and the install fails with a 401 or
  403 error.** fal does not fail the build for a name that it cannot find. It
  keeps the placeholder, and pip receives it as text. Compare the name with the
  output of `fal secrets list`. Also confirm the environment that you deployed
  to.
* **The registry rejects the credentials, but the secret value is correct.** The
  `base64` command adds a newline at the end of its output. This newline becomes
  part of the password. Remove it before you set the secret.
* **A `$` character in a fixed value disappears.** A `$` character starts a
  secret reference. Write `$$` for one literal `$` character. It is better to
  move the value into a secret.
* **The app declares a `secrets` allowlist.** The build reads the same set of
  secrets as the runner. Every name that you use in `requirements` must also
  appear in that list. Refer to
  [Scoping Secrets to an App](manage-secrets-securely.md#scoping-secrets-to-an-app).

Refer to [Secrets](manage-secrets-securely.md) to set,
scope, and rotate secrets. Refer to
[private registries](private-registries.md) to
authenticate to a private Docker registry.

## Dynamic Wheel Selection

When you need different wheels for different Python versions or platforms, use a helper function:

```python theme={null}
def get_package_wheel():
    import sys
    wheels = {
        10: "https://example.com/wheels/mypackage-1.0.0-cp310-cp310-linux_x86_64.whl",
        11: "https://example.com/wheels/mypackage-1.0.0-cp311-cp311-linux_x86_64.whl",
    }
    return wheels[sys.version_info.minor]


class MyApp(fal.App):
    machine_type = "GPU-A100"
    requirements = [
        "torch==2.5.0",
        get_package_wheel(),
    ]
```

> [!NOTE]
>
> Helper functions are evaluated at deploy time on your local machine, so they have access to local environment variables and can make decisions based on the target Python version.

## Experimental Options

### Compile Python Bytecode

Set `compile_bytecode=True` to compile installed Python source files to bytecode (`.pyc`). This can reduce import time during runner startup.

The option requires the `uv` resolver, which `fal.App` uses by default:

```python theme={null}
import fal

class MyModel(fal.App, compile_bytecode=True):
    requirements = ["transformers==4.51.3"]
```

`fal.function` uses pip by default, so set `resolver="uv"` explicitly:

```python theme={null}
@fal.function(
    resolver="uv",
    compile_bytecode=True,
    requirements=["transformers==4.51.3"],
)
def package_version():
    import transformers
    return transformers.__version__
```

Bytecode compilation can lengthen the initial environment build. It does not create caches for [`torch.compile()`](../serverless/optimizations/optimize-startup-with-compiled-caches.md).

***

> [card] [Importing Local Code](import-code.md)
>
> Learn how to bring your local Python packages, modules, and files into the fal runtime.
