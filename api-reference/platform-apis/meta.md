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

# Platform Metadata

> 
Returns platform metadata including webhook IP ranges for allowlisting.

If your infrastructure requires allowlisting IP addresses for incoming webhook
requests, this endpoint provides the current list of IP ranges used by fal.ai
webhooks in CIDR notation.
    

**Authentication:** not required.



## OpenAPI

````yaml GET /meta
openapi: 3.1.0
info:
  title: Platform APIs
  version: v1
  description: fal REST API for programmatic access to platform resources.
servers:
  - url: https://api.fal.ai/v1
    description: Production server
security: []
paths:
  /meta:
    get:
      tags:
        - Meta
      summary: Get platform metadata
      description: >-

        Returns platform metadata including webhook IP ranges for allowlisting.


        If your infrastructure requires allowlisting IP addresses for incoming
        webhook

        requests, this endpoint provides the current list of IP ranges used by
        fal.ai

        webhooks in CIDR notation.
            

        **Authentication:** not required.
      operationId: getMeta
      responses:
        '200':
          description: Platform metadata
          content:
            application/json:
              schema:
                type: object
                properties:
                  webhook_ip_ranges:
                    type: array
                    items:
                      type: string
                    description: IP address ranges (CIDR notation) used by fal.ai webhooks
                required:
                  - webhook_ip_ranges
                description: Platform metadata including webhook IP ranges for allowlisting
                example:
                  webhook_ip_ranges:
                    - 34.123.59.101/32
                    - 34.135.41.243/32
                    - 35.239.83.87/32
                    - 104.198.204.37/32
                    - 34.56.20.205/32
                    - 34.170.94.127/32
                    - 35.224.184.236/32
                    - 136.114.56.197/32
                    - 34.29.37.237/32
                    - 35.225.160.28/32
                    - 34.56.205.145/32
                    - 34.59.170.72/32
                    - 34.10.147.45/32
                    - 104.198.64.245/32
                    - 34.9.1.255/32
              example:
                webhook_ip_ranges:
                  - 34.123.59.101/32
                  - 34.135.41.243/32
                  - 35.239.83.87/32
                  - 104.198.204.37/32
                  - 34.56.20.205/32
                  - 34.170.94.127/32
                  - 35.224.184.236/32
                  - 136.114.56.197/32
                  - 34.29.37.237/32
                  - 35.225.160.28/32
                  - 34.56.205.145/32
                  - 34.59.170.72/32
                  - 34.10.147.45/32
                  - 104.198.64.245/32
                  - 34.9.1.255/32
        '429':
          description: Rate limit exceeded
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: object
                    properties:
                      type:
                        type: string
                        enum:
                          - authorization_error
                          - validation_error
                          - not_found
                          - rate_limited
                          - server_error
                          - not_implemented
                        description: The category of error that occurred
                      message:
                        type: string
                        description: Human-readable error message
                      docs_url:
                        type: string
                        format: uri
                        description: Link to relevant documentation
                      request_id:
                        type: string
                        description: Unique request identifier for debugging
                    required:
                      - type
                      - message
                    description: Error details
                required:
                  - error
                description: Standard error response format
              example:
                error:
                  type: rate_limited
                  message: Rate limit exceeded
        '500':
          description: Internal server error
          content:
            application/json:
              schema:
                type: object
                properties:
                  error:
                    type: object
                    properties:
                      type:
                        type: string
                        enum:
                          - authorization_error
                          - validation_error
                          - not_found
                          - rate_limited
                          - server_error
                          - not_implemented
                        description: The category of error that occurred
                      message:
                        type: string
                        description: Human-readable error message
                      docs_url:
                        type: string
                        format: uri
                        description: Link to relevant documentation
                      request_id:
                        type: string
                        description: Unique request identifier for debugging
                    required:
                      - type
                      - message
                    description: Error details
                required:
                  - error
                description: Standard error response format
              example:
                error:
                  type: server_error
                  message: An unexpected error occurred
      security:
        - {}
        - apiKey: []
components:
  securitySchemes:
    apiKey:
      type: apiKey
      in: header
      name: Authorization
      description: >-
        Key on the `API` preset, or any key whose permissions include the ones
        this API lists. APIs with no required permission accept any key. Prefix
        it with "Key ", e.g. Authorization: Key YOUR_API_KEY

````
