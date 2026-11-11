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

# Create API Key

> Creates a new API key with the specified alias.

Keys are created with granular permissions. Pass `preset` for a named preset
or `permissions` to list them explicitly; omit both to get the `API` preset.

**Important Security Notice:**
The `key_secret` is only returned once at creation time. Store it securely immediately
as it cannot be retrieved again. If lost, you must delete the key and create a new one.

**Key Features:**
- Create API keys programmatically without UI access
- Assign meaningful aliases for key identification
- Scope a key with a named preset or an explicit list of permissions
- Optionally set an expiry with `expires_at`
- Keys are immediately active upon creation

**Common Use Cases:**
- Programmatic key provisioning for CI/CD pipelines
- Self-serve key generation for team members
- Automated key rotation workflows
- Integration with secret management systems

**Required permissions:** `auth:keys:write`. Key presets that include them: `FULL`. See [key permissions](../../../documentation/model-apis/authentication/key-based.md#permissions).



## OpenAPI

````yaml /api-reference/platform-apis/openapi/v1.json post /keys
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
  /keys:
    post:
      tags:
        - Keys
      summary: Create API Key
      description: >-
        Creates a new API key with the specified alias.


        Keys are created with granular permissions. Pass `preset` for a named
        preset

        or `permissions` to list them explicitly; omit both to get the `API`
        preset.


        **Important Security Notice:**

        The `key_secret` is only returned once at creation time. Store it
        securely immediately

        as it cannot be retrieved again. If lost, you must delete the key and
        create a new one.


        **Key Features:**

        - Create API keys programmatically without UI access

        - Assign meaningful aliases for key identification

        - Scope a key with a named preset or an explicit list of permissions

        - Optionally set an expiry with `expires_at`

        - Keys are immediately active upon creation


        **Common Use Cases:**

        - Programmatic key provisioning for CI/CD pipelines

        - Self-serve key generation for team members

        - Automated key rotation workflows

        - Integration with secret management systems


        **Required permissions:** `auth:keys:write`. Key presets that include
        them: `FULL`. See [key
        permissions](https://fal.ai/docs/documentation/model-apis/authentication/key-based#permissions).
      operationId: createApiKey
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                alias:
                  type: string
                  minLength: 1
                  maxLength: 255
                  description: Required friendly name for the API key
                  example: Production Key
                description:
                  type: string
                  maxLength: 1000
                  description: Optional description of what the key is for
                  example: CI deploy key for the main environment
                preset:
                  type: string
                  enum:
                    - FULL
                    - AGENT
                    - API
                    - DEPLOY
                    - READONLY
                    - BILLING
                    - COMPUTE
                    - SERVERLESS
                  description: >-
                    Named permission preset to apply. Defaults to "API" when
                    neither preset nor permissions is given. Mutually exclusive
                    with permissions.
                  example: API
                permissions:
                  type: array
                  items:
                    type: string
                  minItems: 1
                  description: >-
                    Explicit list of permissions to grant instead of a preset.
                    Mutually exclusive with preset.
                  example:
                    - models:requests:read
                    - models:requests:write
                expires_at:
                  type: string
                  format: date-time
                  description: ISO8601 timestamp after which the key stops working
                  example: '2025-07-15T12:00:00Z'
              required:
                - alias
              additionalProperties: false
            example:
              alias: Production Key
              preset: API
      responses:
        '201':
          description: >-
            API key created successfully. Store the key_secret securely - it
            will not be shown again.
          content:
            application/json:
              schema:
                type: object
                properties:
                  key_id:
                    type: string
                    description: Unique identifier for the newly created API key
                    example: abc123def456
                  key_secret:
                    type: string
                    description: >-
                      Secret portion of the API key. IMPORTANT: This is only
                      returned once at creation time and cannot be retrieved
                      again.
                    example: sk_live_abc123...
                  key:
                    type: string
                    description: >-
                      Full API key in the format 'key_id:key_secret'. Use this
                      value directly for API authorization. IMPORTANT: This is
                      only returned once at creation time and cannot be
                      retrieved again.
                    example: abc123def456:sk_live_abc123...
                required:
                  - key_id
                  - key_secret
                  - key
                description: >-
                  Response containing the newly created API key credentials. The
                  key_secret is only returned once.
              example:
                key_id: abc123def456
                key_secret: sk_live_abc123def456xyz789
                key: abc123def456:sk_live_abc123def456xyz789
        '400':
          description: Invalid request parameters
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
                  type: validation_error
                  message: Invalid request parameters
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
