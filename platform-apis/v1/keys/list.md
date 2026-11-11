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

# List API Keys

> Returns a list of all API keys belonging to the authenticated user's workspace.

**Key Features:**
- View all API keys with their aliases and creation dates
- See how each key is permissioned via `mode`, `preset` and `permissions`
- Optionally expand to include creator information
- Paginated results for workspaces with many keys

**Key Modes:**
- `preset`: created from a named permission preset, reported in `preset`
- `custom`: created with an explicit list of permissions
- `legacy`: predates granular permissions and carries a coarse scope

**Expansion Options:**
- `expand=creator_info`: Include creator_nickname and creator_email for each key

**Common Use Cases:**
- Audit existing API keys
- Find keys by alias
- Monitor key creation activity
- Build key management interfaces

**Required permissions:** `auth:keys:read`. Key presets that include them: `BILLING`, `FULL`. See [key permissions](../../../documentation/model-apis/authentication/key-based.md#permissions).



## OpenAPI

````yaml /api-reference/platform-apis/openapi/v1.json get /keys
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
    get:
      tags:
        - Keys
      summary: List API Keys
      description: >-
        Returns a list of all API keys belonging to the authenticated user's
        workspace.


        **Key Features:**

        - View all API keys with their aliases and creation dates

        - See how each key is permissioned via `mode`, `preset` and
        `permissions`

        - Optionally expand to include creator information

        - Paginated results for workspaces with many keys


        **Key Modes:**

        - `preset`: created from a named permission preset, reported in `preset`

        - `custom`: created with an explicit list of permissions

        - `legacy`: predates granular permissions and carries a coarse scope


        **Expansion Options:**

        - `expand=creator_info`: Include creator_nickname and creator_email for
        each key


        **Common Use Cases:**

        - Audit existing API keys

        - Find keys by alias

        - Monitor key creation activity

        - Build key management interfaces


        **Required permissions:** `auth:keys:read`. Key presets that include
        them: `BILLING`, `FULL`. See [key
        permissions](https://fal.ai/docs/documentation/model-apis/authentication/key-based#permissions).
      operationId: listApiKeys
      parameters:
        - schema:
            type: integer
            minimum: 1
            description: >-
              Maximum number of items to return. Actual maximum depends on query
              type and expansion parameters.
            example: 50
          required: false
          description: >-
            Maximum number of items to return. Actual maximum depends on query
            type and expansion parameters.
          name: limit
          in: query
        - schema:
            type: string
            description: Pagination cursor from previous response. Encodes the page number.
            example: Mg==
          required: false
          description: Pagination cursor from previous response. Encodes the page number.
          name: cursor
          in: query
        - schema:
            anyOf:
              - type: string
              - type: array
                items:
                  type: string
            description: >-
              Fields to expand in the response. Available: creator_info
              (includes creator_nickname and creator_email)
            example:
              - creator_info
          required: false
          description: >-
            Fields to expand in the response. Available: creator_info (includes
            creator_nickname and creator_email)
          name: expand
          style: form
          explode: true
          in: query
      responses:
        '200':
          description: Successfully retrieved API keys
          content:
            application/json:
              schema:
                type: object
                properties:
                  next_cursor:
                    type:
                      - string
                      - 'null'
                    description: Cursor for the next page of results, null if no more pages
                  has_more:
                    type: boolean
                    description: >-
                      Boolean indicating if more results are available
                      (convenience field derived from next_cursor)
                  keys:
                    type: array
                    items:
                      type: object
                      properties:
                        key_id:
                          type: string
                          description: Unique identifier for the API key
                          example: abc123def456
                        alias:
                          type: string
                          description: User-provided friendly name for the key
                          example: Production Key
                        scope:
                          type: string
                          enum:
                            - API
                            - ADMIN
                          description: >-
                            Coarse scope of a legacy API key. Deprecated:
                            superseded by mode, preset and permissions, and
                            omitted for keys that carry granular permissions.
                          example: API
                          deprecated: true
                        created_at:
                          type: string
                          description: ISO8601 timestamp when the key was created
                          example: '2025-01-15T12:00:00Z'
                        description:
                          type: string
                          description: User-provided description of what the key is for
                          example: CI deploy key for the main environment
                        mode:
                          type: string
                          enum:
                            - legacy
                            - preset
                            - custom
                          description: >-
                            How the key's permissions are expressed: a named
                            preset, a custom permission list, or a legacy scope.
                          example: preset
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
                            Preset the key was created from, present when mode
                            is preset
                          example: API
                        permissions:
                          type: array
                          items:
                            type: string
                          minItems: 1
                          description: >-
                            Resolved permissions, present for preset and custom
                            keys
                          example:
                            - models:requests:read
                            - models:requests:write
                        expires_at:
                          type:
                            - string
                            - 'null'
                          description: >-
                            ISO8601 timestamp when the key expires, or null if
                            it doesn't
                          example: '2025-07-15T12:00:00Z'
                        creator_nickname:
                          type: string
                          description: >-
                            Nickname of the user who created this key (when
                            expanded)
                          example: developer
                        creator_email:
                          type: string
                          description: >-
                            Email of the user who created this key (when
                            expanded)
                          example: developer@example.com
                      required:
                        - key_id
                        - alias
                        - created_at
                        - mode
                        - expires_at
                      description: API key information
                    description: Array of API keys belonging to the authenticated user
                required:
                  - next_cursor
                  - has_more
                  - keys
                description: Response containing a list of API keys with pagination support
              example:
                keys:
                  - key_id: abc123def456
                    alias: Production Key
                    created_at: '2025-01-15T12:00:00Z'
                    mode: preset
                    preset: API
                    permissions:
                      - account:settings:read
                      - assets:read
                      - assets:write
                      - auth:tokens:create
                      - models:list
                      - models:metrics:read
                      - models:requests:cancel
                      - models:requests:read
                      - models:requests:share
                      - models:requests:submit
                      - serverless:metrics:read
                      - serverless:requests:read
                      - workflows:read
                      - workflows:submit
                    expires_at: null
                  - key_id: xyz789ghi012
                    alias: Inference Key
                    created_at: '2025-01-10T09:30:00Z'
                    mode: custom
                    permissions:
                      - models:list
                      - models:requests:read
                      - models:requests:submit
                    expires_at: null
                next_cursor: null
                has_more: false
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
        - billingApiKey: []
components:
  securitySchemes:
    billingApiKey:
      type: apiKey
      in: header
      name: Authorization
      description: >-
        Key on the `BILLING` preset, or any key whose permissions include the
        ones this API lists. Prefix it with "Key ", e.g. Authorization: Key
        YOUR_API_KEY

````
