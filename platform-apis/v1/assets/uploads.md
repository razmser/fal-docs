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

# Upload asset

> Upload asset for the authenticated user's fal Assets library.

**Required permissions:** `assets:write`. Key presets that include them: `API`, `SERVERLESS`, `FULL`. See [key permissions](../../../documentation/model-apis/authentication/key-based.md#permissions).



## OpenAPI

````yaml /api-reference/platform-apis/openapi/v1.json post /assets/uploads
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
  /assets/uploads:
    post:
      tags:
        - Assets
      summary: Upload asset
      description: >-
        Upload asset for the authenticated user's fal Assets library.


        **Required permissions:** `assets:write`. Key presets that include them:
        `API`, `SERVERLESS`, `FULL`. See [key
        permissions](https://fal.ai/docs/documentation/model-apis/authentication/key-based#permissions).
      operationId: uploadAsset
      parameters:
        - schema:
            type: string
            description: Optional idempotency key for safe request retries
            example: 550e8400-e29b-41d4-a716-446655440000
          required: false
          description: Optional idempotency key for safe request retries
          name: Idempotency-Key
          in: header
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                url:
                  type: string
                  format: uri
                  description: fal-hosted media URL to ingest into the asset library
                type:
                  type: string
                  enum:
                    - image
                    - video
                    - audio
                    - 3d
                  description: Media type for the uploaded asset
                prompt:
                  type:
                    - string
                    - 'null'
                  minLength: 1
                  maxLength: 2000
                  description: >-
                    Optional caller-provided caption or description to index
                    with the uploaded asset
                collection_id:
                  type:
                    - string
                    - 'null'
                  description: Optional manual collection ID to add the uploaded asset to
                favorite:
                  type: boolean
                  default: false
                  description: Whether to favorite the uploaded asset immediately
                tag_ids:
                  type: array
                  items:
                    type: string
                  default: []
                  description: Tag IDs to assign to the uploaded asset
              required:
                - url
                - type
            example:
              url: >-
                https://v3b.fal.media/files/b/0a9b4900/PDbTGyzqRh1aijW2WQiY9_opengraph-1%20%281%29.png
              type: image
              prompt: Cinematic portrait of a character
              collection_id: d8b6elcregj72v34jr8g
              favorite: true
              tag_ids:
                - d9k7q2m4n6p8r1s3t5uv
      responses:
        '201':
          description: Upload ingested
          content:
            application/json:
              schema:
                type: object
                properties:
                  asset:
                    type: object
                    properties:
                      asset_id:
                        type:
                          - string
                          - 'null'
                        description: Asset ID (canonical catalog identity)
                        example: d8b6elcregj72v34jr8g
                      vector_id:
                        type: string
                        description: Vector ID. Prefer asset_id for addressing
                        example: b1a2b4a5-cb89-51dc-a108-6dbfd8e742fc
                      request_id:
                        type:
                          - string
                          - 'null'
                        description: Request ID
                        example: 019e6d0a-e5be-7b82-b329-35ae64296902
                      url:
                        type:
                          - string
                          - 'null'
                        description: Media URL
                        example: >-
                          https://v3b.fal.media/files/b/0a9b4900/PDbTGyzqRh1aijW2WQiY9_opengraph-1%20%281%29.png
                      type:
                        type: string
                        enum:
                          - image
                          - video
                          - audio
                          - 3d
                        description: Asset media type
                      title:
                        type: string
                        description: Display title
                        example: Portrait
                      endpoint:
                        type:
                          - string
                          - 'null'
                        description: Model endpoint that produced the asset
                        example: fal-ai/flux/dev
                      created_at:
                        type:
                          - string
                          - 'null'
                        description: Asset creation time
                        example: '2026-05-23T20:00:00.000Z'
                      source:
                        type:
                          - string
                          - 'null'
                        description: Asset source
                        example: upload
                      prompt:
                        type:
                          - string
                          - 'null'
                        description: Saved prompt or description
                        example: cinematic portrait
                      width:
                        type:
                          - number
                          - 'null'
                        description: Width in pixels
                        example: 1024
                      height:
                        type:
                          - number
                          - 'null'
                        description: Height in pixels
                        example: 1024
                      content_type:
                        type:
                          - string
                          - 'null'
                        description: Media content type
                        example: image/png
                      is_favorited:
                        type: boolean
                        description: Whether the asset is favorited
                        example: false
                      collection_ids:
                        type: array
                        items:
                          type: string
                        description: Collections containing this asset
                        example:
                          - d8b6elcregj72v34jr8g
                      tags:
                        type: array
                        items:
                          type: object
                          properties:
                            id:
                              type: string
                              description: Tag ID
                              example: d9k7q2m4n6p8r1s3t5uv
                            name:
                              type: string
                              description: Tag name
                              example: moodboard
                            created_at:
                              type:
                                - string
                                - 'null'
                              description: Tag creation time
                              example: '2026-05-23T20:00:00.000Z'
                          required:
                            - id
                            - name
                            - created_at
                          description: Asset tag
                        description: Assigned tags
                      similarity:
                        type:
                          - number
                          - 'null'
                        description: Semantic similarity score when applicable
                        example: 0.92
                    required:
                      - asset_id
                      - vector_id
                      - request_id
                      - url
                      - type
                      - title
                      - endpoint
                      - created_at
                      - source
                      - prompt
                      - width
                      - height
                      - content_type
                      - is_favorited
                      - collection_ids
                      - tags
                      - similarity
                    description: Asset details
                required:
                  - asset
              example:
                asset:
                  asset_id: d8b6elcregj72v34jr8g
                  vector_id: b1a2b4a5-cb89-51dc-a108-6dbfd8e742fc
                  request_id: 019e6d0a-e5be-7b82-b329-35ae64296902
                  url: >-
                    https://v3b.fal.media/files/b/0a9b4900/PDbTGyzqRh1aijW2WQiY9_opengraph-1%20%281%29.png
                  type: image
                  title: Cinematic portrait
                  endpoint: fal-ai/flux/dev
                  created_at: '2026-05-23T20:00:00.000Z'
                  source: upload
                  prompt: cinematic portrait of a character
                  width: 1024
                  height: 1024
                  content_type: image/png
                  is_favorited: false
                  collection_ids:
                    - d8b6elcregj72v34jr8g
                  tags:
                    - id: d9k7q2m4n6p8r1s3t5uv
                      name: moodboard
                      created_at: '2026-05-23T20:00:00.000Z'
                  similarity: 0.92
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
        '404':
          description: Resource not found
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
                  type: not_found
                  message: Resource not found
        '409':
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
        '422':
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
        '502':
          description: Upstream asset service error
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
                  message: Upstream asset service failed
      security:
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
