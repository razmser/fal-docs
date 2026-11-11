> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../../../documentation/quickstart.md) and the [Model APIs overview](../../../../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../../../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../../../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../../../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../../../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../../../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../../../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../../../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../../../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../../../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../../../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../../../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Browse assets in a collection

> Browse assets in a collection for the authenticated user's fal Assets library.

**Required permissions:** `assets:read`. Key presets that include them: `API`, `SERVERLESS`, `READONLY`, `FULL`. See [key permissions](../../../../documentation/model-apis/authentication/key-based.md#permissions).



## OpenAPI

````yaml /api-reference/platform-apis/openapi/v1.json get /assets/collections/{collection_id}/assets
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
  /assets/collections/{collection_id}/assets:
    get:
      tags:
        - Assets
      summary: Browse assets in a collection
      description: >-
        Browse assets in a collection for the authenticated user's fal Assets
        library.


        **Required permissions:** `assets:read`. Key presets that include them:
        `API`, `SERVERLESS`, `READONLY`, `FULL`. See [key
        permissions](https://fal.ai/docs/documentation/model-apis/authentication/key-based#permissions).
      operationId: listAssetCollectionAssets
      parameters:
        - schema:
            type: string
            minLength: 1
            description: Collection ID
            example: d8b6elcregj72v34jr8g
          required: true
          description: Collection ID
          name: collection_id
          in: path
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
            description: Text query for hybrid semantic search
            example: cinematic portrait
          required: false
          description: Text query for hybrid semantic search
          name: q
          in: query
        - schema:
            type: string
            format: uri
            description: fal-hosted image URL to use for semantic image search
            example: >-
              https://v3b.fal.media/files/b/0a9b4900/PDbTGyzqRh1aijW2WQiY9_opengraph-1%20%281%29.png
          required: false
          description: fal-hosted image URL to use for semantic image search
          name: search_image_url
          in: query
        - schema:
            type: string
            format: uri
            description: fal-hosted video URL to use for semantic video search
            example: https://fal.media/files/example.mp4
          required: false
          description: fal-hosted video URL to use for semantic video search
          name: search_video_url
          in: query
        - schema:
            type:
              - array
              - 'null'
            items:
              type: string
              enum:
                - image
                - video
                - audio
                - 3d
              description: Asset media type
            default: []
            description: Filter by one or more media types
            example:
              - image
              - video
          required: false
          description: Filter by one or more media types
          name: media_type
          in: query
        - schema:
            type:
              - array
              - 'null'
            items:
              type: string
              enum:
                - upload
                - response
              description: Indexed asset source
            default: []
            description: Filter by one or more indexed sources
            example:
              - upload
              - response
          required: false
          description: Filter by one or more indexed sources
          name: source
          in: query
        - schema:
            type: string
            enum:
              - all-media
              - uploads
              - favorites
              - generated
            default: all-media
            description: Asset library section to browse
            example: all-media
          required: false
          description: Asset library section to browse
          name: section
          in: query
        - schema:
            type:
              - array
              - 'null'
            items:
              type: string
            default: []
            description: Character identifiers to use as @mention semantic filters
            example:
              - alice
          required: false
          description: Character identifiers to use as @mention semantic filters
          name: character_identifier
          in: query
        - schema:
            type:
              - array
              - 'null'
            items:
              type: string
            default: []
            description: Tag IDs to filter by
            example:
              - d9k7q2m4n6p8r1s3t5uv
          required: false
          description: Tag IDs to filter by
          name: tag_id
          in: query
        - schema:
            type: string
            enum:
              - any
              - all
            default: any
            description: Whether tag filters match any tag or all tags
            example: any
          required: false
          description: Whether tag filters match any tag or all tags
          name: tag_mode
          in: query
      responses:
        '200':
          description: Collection assets
          content:
            application/json:
              schema:
                type: object
                properties:
                  assets:
                    type: array
                    items:
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
                      description: Asset item
                    description: Asset results
                  next_cursor:
                    type:
                      - string
                      - 'null'
                    description: Cursor for the next page
                  has_more:
                    type: boolean
                    description: Whether more results are available
                  total_count:
                    type:
                      - number
                      - 'null'
                    description: Exact total when available; null when unavailable
                  scope_truncated:
                    type: boolean
                    description: >-
                      True when the requested scope (favorites, tags, or
                      collection membership) exceeded the maximum number of
                      assets a single search can cover, so results were drawn
                      from a partial slice of it and some matches are missing.
                      Narrow the scope for complete results. `total_count`
                      remains accurate for the results that were returned.
                required:
                  - assets
                  - next_cursor
                  - has_more
                  - total_count
                  - scope_truncated
                description: Paginated asset browse response
              example:
                assets:
                  - asset_id: d8b6elcregj72v34jr8g
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
                next_cursor: null
                has_more: false
                total_count: 1
                scope_truncated: false
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
