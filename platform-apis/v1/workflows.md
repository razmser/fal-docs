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

# List user workflows

> 
List workflows for the authenticated user with optional search and filtering.

**Features:**
- Paginated results with cursor-based pagination
- Search by workflow name or title
- Filter by model endpoints used in the workflow

**Authentication:** Required. Returns only workflows owned by the authenticated user.

**Common Use Cases:**
- Display user's workflow library
- Search for specific workflows
- Find workflows using particular models
    

**Required permissions:** `workflows:read`. Key presets that include them: `API`, `SERVERLESS`, `READONLY`, `FULL`. See [key permissions](../../documentation/model-apis/authentication/key-based.md#permissions).



## OpenAPI

````yaml /api-reference/platform-apis/openapi/v1.json get /workflows
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
  /workflows:
    get:
      tags:
        - Workflows
      summary: List user workflows
      description: >-

        List workflows for the authenticated user with optional search and
        filtering.


        **Features:**

        - Paginated results with cursor-based pagination

        - Search by workflow name or title

        - Filter by model endpoints used in the workflow


        **Authentication:** Required. Returns only workflows owned by the
        authenticated user.


        **Common Use Cases:**

        - Display user's workflow library

        - Search for specific workflows

        - Find workflows using particular models
            

        **Required permissions:** `workflows:read`. Key presets that include
        them: `API`, `SERVERLESS`, `READONLY`, `FULL`. See [key
        permissions](https://fal.ai/docs/documentation/model-apis/authentication/key-based#permissions).
      operationId: listWorkflows
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
            type: string
            description: Search by workflow name or title
            example: image generation
          required: false
          description: Search by workflow name or title
          name: search
          in: query
        - schema:
            anyOf:
              - type: string
              - type: array
                items:
                  type: string
            description: >-
              Filter by model endpoint IDs used in the workflow. Can be a single
              value or comma-separated values.
            example:
              - fal-ai/flux/dev
          required: false
          description: >-
            Filter by model endpoint IDs used in the workflow. Can be a single
            value or comma-separated values.
          name: used_endpoint_ids
          style: form
          explode: true
          in: query
      responses:
        '200':
          description: Successfully retrieved workflows
          content:
            application/json:
              schema:
                type: object
                properties:
                  workflows:
                    type: array
                    items:
                      type: object
                      properties:
                        name:
                          type: string
                          description: >-
                            Unique workflow name/slug within the user's
                            namespace
                          example: my-image-workflow
                        title:
                          type: string
                          description: Human-readable workflow title
                          example: My Image Generation Workflow
                        user_nickname:
                          type: string
                          description: Display name/username of the owner
                          example: johndoe
                        created_at:
                          type: string
                          description: ISO8601 timestamp of workflow creation
                          example: '2024-01-15T10:30:00Z'
                        thumbnail_url:
                          type: string
                          description: URL to the workflow thumbnail image
                          example: https://fal.ai/workflows/thumb_abc123.png
                        description:
                          type: string
                          description: Brief description of what the workflow does
                          example: Generate high-quality images using FLUX model
                        tags:
                          type: array
                          items:
                            type: string
                            example: image-generation
                          description: Tags associated with the workflow
                          example:
                            - image-generation
                            - ai
                            - flux
                        endpoint_ids:
                          type: array
                          items:
                            type: string
                            example: fal-ai/flux/dev
                          description: List of model endpoint IDs used in this workflow
                          example:
                            - fal-ai/flux/dev
                            - fal-ai/face-swap
                      required:
                        - name
                        - title
                        - user_nickname
                        - created_at
                        - tags
                        - endpoint_ids
                      description: Workflow information
                    description: Array of workflow items
                    example:
                      - name: my-image-workflow
                        title: My Image Generation Workflow
                        user_nickname: johndoe
                        created_at: '2024-01-15T10:30:00Z'
                        thumbnail_url: https://fal.ai/workflows/thumb_abc123.png
                        description: Generate high-quality images using FLUX model
                        tags:
                          - image-generation
                          - ai
                          - flux
                        endpoint_ids:
                          - fal-ai/flux/dev
                          - fal-ai/face-swap
                  next_cursor:
                    type:
                      - string
                      - 'null'
                    description: Cursor for the next page of results, null if no more pages
                    example: eyJvZmZzZXQiOjEwfQ==
                  has_more:
                    type: boolean
                    description: Whether more results are available
                    example: true
                  total:
                    type: integer
                    description: Total number of workflows matching the query
                    example: 42
                required:
                  - workflows
                  - next_cursor
                  - has_more
                description: Response containing workflow data with pagination support
              example:
                workflows:
                  - name: my-image-workflow
                    title: My Image Generation Workflow
                    user_nickname: johndoe
                    created_at: '2025-01-15T12:00:00Z'
                    thumbnail_url: https://fal.media/files/example.jpg
                    description: A workflow for generating images
                    tags:
                      - image
                      - generation
                    endpoint_ids:
                      - fal-ai/flux/dev
                next_cursor: null
                has_more: false
                total: 1
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
        - readonlyApiKey: []
components:
  securitySchemes:
    readonlyApiKey:
      type: apiKey
      in: header
      name: Authorization
      description: >-
        Key on the `READONLY` preset, or any key whose permissions include the
        ones this API lists. Prefix it with "Key ", e.g. Authorization: Key
        YOUR_API_KEY

````
