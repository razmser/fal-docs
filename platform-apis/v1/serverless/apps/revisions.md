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

# Application Revisions

> Returns the revision history for an application, newest first: every
deployed version with its creation time, whether it is currently serving
traffic, and a deployment status derived from recent deployment events.

**Use cases:**
- Incident correlation: answer "did this regression start with a deploy?"
  by lining revision boundaries up against error/latency changes from the
  analytics endpoint
- Rollback context: identify the previous known-good revision

`status` and `deployed_by` are derived from the last 30 days of
deployment events; older revisions return null for both. `message` and
`annotations` are the deploy-time metadata set via `fal deploy
--message`/`--annotation`; both are null when the deploy did not set them.

**Authentication:** Required via API key. Only the app owner can query it.

**Required permissions:** `serverless:apps:read`. Key presets that include them: `SERVERLESS`, `DEPLOY`, `READONLY`, `FULL`. See [key permissions](../../../../documentation/model-apis/authentication/key-based.md#permissions).



## OpenAPI

````yaml /api-reference/platform-apis/openapi/v1.json get /serverless/apps/{owner}/{name}/revisions
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
  /serverless/apps/{owner}/{name}/revisions:
    get:
      tags:
        - Serverless
        - Apps
      summary: Application Revisions
      description: >-
        Returns the revision history for an application, newest first: every

        deployed version with its creation time, whether it is currently serving

        traffic, and a deployment status derived from recent deployment events.


        **Use cases:**

        - Incident correlation: answer "did this regression start with a
        deploy?"
          by lining revision boundaries up against error/latency changes from the
          analytics endpoint
        - Rollback context: identify the previous known-good revision


        `status` and `deployed_by` are derived from the last 30 days of

        deployment events; older revisions return null for both. `message` and

        `annotations` are the deploy-time metadata set via `fal deploy

        --message`/`--annotation`; both are null when the deploy did not set
        them.


        **Authentication:** Required via API key. Only the app owner can query
        it.


        **Required permissions:** `serverless:apps:read`. Key presets that
        include them: `SERVERLESS`, `DEPLOY`, `READONLY`, `FULL`. See [key
        permissions](https://fal.ai/docs/documentation/model-apis/authentication/key-based#permissions).
      operationId: serverlessListAppRevisions
      parameters:
        - schema:
            type: string
            description: Username of the app owner
            example: user_123
          required: true
          description: Username of the app owner
          name: owner
          in: path
        - schema:
            type: string
            description: Application name
            example: my-app
          required: true
          description: Application name
          name: name
          in: path
        - schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 50
            description: Maximum number of revisions to return per page (1-100)
            example: 50
          required: false
          description: Maximum number of revisions to return per page (1-100)
          name: limit
          in: query
        - schema:
            type: string
            description: Pagination cursor from previous response
            example: Mg==
          required: false
          description: Pagination cursor from previous response
          name: cursor
          in: query
      responses:
        '200':
          description: Successfully retrieved application revisions
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
                  endpoint_id:
                    type: string
                    description: Canonical endpoint identifier ('<owner>/<name>')
                    example: user_123/my-app
                  revisions:
                    type: array
                    items:
                      type: object
                      properties:
                        revision_id:
                          type: string
                          description: Unique revision identifier
                          example: my-app-a1b2c3d4
                        created_at:
                          type: string
                          description: Revision creation timestamp (UTC ISO8601)
                          example: '2026-07-01T12:00:00Z'
                        is_current:
                          type: boolean
                          description: >-
                            Whether this revision is the one currently serving
                            traffic
                          example: true
                        message:
                          type:
                            - string
                            - 'null'
                          description: >-
                            Freeform message attached to this revision at deploy
                            time (fal deploy --message). Null when the deploy
                            did not set one.
                          example: a1b2c3d fix cold-start
                        annotations:
                          type:
                            - object
                            - 'null'
                          additionalProperties:
                            type: string
                          description: >-
                            Custom key/value annotations attached to this
                            revision at deploy time (fal deploy --annotation
                            KEY=VALUE). Null when the deploy did not set any.
                          example:
                            GIT_SHA: a1b2c3d4
                        status:
                          type:
                            - string
                            - 'null'
                          enum:
                            - deployed
                            - failed
                            - deploying
                          description: >-
                            Deployment status derived from recent deployment
                            events. Null when no deployment events are available
                            for this revision (e.g. revisions older than the
                            event retention window).
                          example: deployed
                        deployed_by:
                          type:
                            - string
                            - 'null'
                          description: >-
                            Nickname or display name of the user who deployed
                            this revision, when known from recent deployment
                            events
                          example: user_123
                      required:
                        - revision_id
                        - created_at
                        - is_current
                        - message
                        - annotations
                        - status
                        - deployed_by
                      additionalProperties: false
                      description: A deployed revision of a serverless application
                    description: Application revisions, newest first
                required:
                  - next_cursor
                  - has_more
                  - endpoint_id
                  - revisions
                additionalProperties: false
                description: Revision history for an application
              example:
                endpoint_id: user_123/my-app
                revisions:
                  - revision_id: my-app-a1b2c3d4
                    created_at: '2026-07-01T12:00:00Z'
                    is_current: true
                    message: a1b2c3d fix cold-start
                    annotations:
                      GIT_SHA: a1b2c3d4
                    status: deployed
                    deployed_by: user_123
                next_cursor: null
                has_more: false
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
