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

# Authentication

> Platform APIs require API keys for secure access to your user or team's data.

## Generating API Keys

Navigate to the dashboard keys page and generate a key from the UI: [fal.ai/dashboard/keys](https://fal.ai/dashboard/keys)

## Permissions

Platform APIs require different key permissions.

[Learn more about key-based authentication and permissions](../../documentation/model-apis/authentication/key-based.md#permissions).

> [card] API preset
>
> Most Platform APIs accept a key with the **`API`** preset. This preset
> covers model discovery, model analytics, and the requests you submit.

> [card] Other presets
>
> Some Platform APIs read sensitive data and need more permissions. Billing
> endpoints need the **`BILLING`** preset, compute endpoints need the
> **`COMPUTE`** preset, and a key with the **`FULL`** preset reaches every
> endpoint.

> [!NOTE]
>
> Match the preset to the surface you call. If you are not sure, start with a key on the `API` preset. You can create a key with more permissions later.

> [!WARNING]
>
> API keys should be kept secure and never exposed in client-side code or public repositories.

## Authentication Format

Include your API key in the `Authorization` header with the `Key` prefix:

```bash theme={null}
Authorization: Key YOUR_API_KEY
```

The header is the same for every preset. Only the permissions of the key change.

## Usage Examples

### cURL

Using a key on the `API` preset for model listing:

```bash theme={null}
curl -X GET "https://api.fal.ai/v1/models?limit=10" \
  -H "Authorization: Key YOUR_API_KEY"
```

Using a key on the `BILLING` preset for usage data:

```bash theme={null}
curl -X GET "https://api.fal.ai/v1/models/usage" \
  -H "Authorization: Key YOUR_BILLING_API_KEY"
```

### Python

Using a key on the `API` preset:

```python theme={null}
import requests

headers = {
    "Authorization": "Key YOUR_API_KEY"
}

response = requests.get(
    "https://api.fal.ai/v1/models",
    headers=headers,
    params={"limit": 10}
)

print(response.json())
```

### JavaScript

Using a key on the `API` preset:

```javascript theme={null}
const response = await fetch('https://api.fal.ai/v1/models?limit=10', {
  headers: {
    'Authorization': 'Key YOUR_API_KEY'
  }
});

const data = await response.json();
console.log(data);
```

## Best Practices

* Store API keys in environment variables
* Use the preset with the fewest permissions for your use case
* Rotate keys regularly
* Keep `FULL` preset keys secure and never expose them client-side

## Troubleshooting

### 401 Unauthorized

* Verify your API key is correct
* Ensure the `Authorization` header includes the `Key` prefix
* Check that your API key hasn't been revoked

### 403 Forbidden

The key does not carry the permission the endpoint requires. Create a key on the preset that covers the surface you call, such as `BILLING` for billing endpoints or `COMPUTE` for compute endpoints.
