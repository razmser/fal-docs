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

# OpenAPI Schema

> The fal Platform API is documented using the OpenAPI 3.1 specification format.

## Download

Download the complete OpenAPI schema to integrate with your tools and workflows:

> [card] [OpenAPI Specification](https://api.fal.ai/v1/openapi.json)
>
> Download the OpenAPI 3.1 JSON schema

## Use Cases

The OpenAPI schema enables you to:

* **Generate Client SDKs**: Use tools like OpenAPI Generator to create client libraries in your preferred language
* **API Testing**: Import into testing tools like Postman or Insomnia
* **Code Generation**: Generate type definitions and API interfaces
* **Documentation**: Use with documentation generators and API explorers
* **Validation**: Validate API requests and responses against the schema

## Schema URL

```
https://api.fal.ai/v1/openapi.json
```

## Integration Examples

### Postman

1. Open Postman
2. Click **Import**
3. Select **Link** and paste the schema URL
4. Postman will generate a collection from the OpenAPI spec

### OpenAPI Generator

Generate a client library using the OpenAPI Generator:

```bash theme={null}
openapi-generator-cli generate \
  -i https://api.fal.ai/v1/openapi.json \
  -g python \
  -o ./fal-api-client
```

### Swagger UI

View the API documentation in Swagger UI:

```bash theme={null}
docker run -p 8080:8080 \
  -e SWAGGER_JSON_URL=https://api.fal.ai/v1/openapi.json \
  swaggerapi/swagger-ui
```

## Specification Details

* **Version**: OpenAPI 3.1.0
* **Format**: JSON
* **Authentication**: API key. The required preset depends on the endpoint. See [Key permissions](../../documentation/model-apis/authentication/key-based.md#permissions).
* **Base URL**: `https://api.fal.ai/v1`
