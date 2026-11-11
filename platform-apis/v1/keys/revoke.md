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

# Revoke API Key

> Revokes an API key by its ID, so it can no longer be used. This action is irreversible.

The key stops working immediately and drops out of `GET /v1/keys`, but the
record is retained with when it was revoked and by whom.

**Requirements:**
- Key must belong to the authenticated user's workspace

**Key Features:**
- Permanently revoke API key access while retaining the audit record
- Idempotent operation (safe to retry)
- Optional Idempotency-Key header for safe retries

**Important:**
- This action cannot be undone
- Any applications using this key will immediately lose access
- Returns 204 even if the key doesn't exist or is already revoked

**Common Use Cases:**
- Revoke compromised keys
- Clean up unused keys
- Implement key rotation (revoke old, create new)
- Offboard team members

**Required permissions:** `auth:keys:write`. Key presets that include them: `FULL`. See [key permissions](../../../documentation/model-apis/authentication/key-based.md#permissions).



## OpenAPI

````yaml /api-reference/platform-apis/openapi/v1.json post /keys/{key_id}/revoke
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
  /keys/{key_id}/revoke:
    post:
      tags:
        - Keys
      summary: Revoke API Key
      description: >-
        Revokes an API key by its ID, so it can no longer be used. This action
        is irreversible.


        The key stops working immediately and drops out of `GET /v1/keys`, but
        the

        record is retained with when it was revoked and by whom.


        **Requirements:**

        - Key must belong to the authenticated user's workspace


        **Key Features:**

        - Permanently revoke API key access while retaining the audit record

        - Idempotent operation (safe to retry)

        - Optional Idempotency-Key header for safe retries


        **Important:**

        - This action cannot be undone

        - Any applications using this key will immediately lose access

        - Returns 204 even if the key doesn't exist or is already revoked


        **Common Use Cases:**

        - Revoke compromised keys

        - Clean up unused keys

        - Implement key rotation (revoke old, create new)

        - Offboard team members


        **Required permissions:** `auth:keys:write`. Key presets that include
        them: `FULL`. See [key
        permissions](https://fal.ai/docs/documentation/model-apis/authentication/key-based#permissions).
      operationId: revokeApiKey
      parameters:
        - schema:
            type: string
            minLength: 1
            description: Unique identifier of the API key to revoke
            example: abc123def456
          required: true
          description: Unique identifier of the API key to revoke
          name: key_id
          in: path
        - schema:
            type: string
            description: Optional idempotency key for safe request retries
            example: 550e8400-e29b-41d4-a716-446655440000
          required: false
          description: Optional idempotency key for safe request retries
          name: Idempotency-Key
          in: header
      responses:
        '204':
          description: API key revoked successfully (or already revoked)
        '401':
          description: Authentication required
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
                  type: authorization_error
                  message: Authentication required
        '403':
          description: Access denied
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
                  type: authorization_error
                  message: Access denied
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
        - fullApiKey: []
components:
  securitySchemes:
    fullApiKey:
      type: apiKey
      in: header
      name: Authorization
      description: >-
        Key on the `FULL` preset, or any key whose permissions include the ones
        this API lists. Prefix it with "Key ", e.g. Authorization: Key
        YOUR_API_KEY

````
