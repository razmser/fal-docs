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

# List Compute Instances

> Returns a list of all compute instances belonging to the authenticated user's workspace.

**Requirements:**
- Requires compute permissions (extra_permissions.compute = true)

**Key Features:**
- View all instances regardless of status
- Includes instance configuration, region, and current status
- Paginated results for large instance lists

**Common Use Cases:**
- Monitor all active compute resources
- Check instance status and availability
- Audit compute resource usage
- Build compute management dashboards

See [fal.ai docs](../../../../documentation/compute.md) for more details.

**Required permissions:** `compute:instances:read`. Key presets that include them: `COMPUTE`, `READONLY`, `FULL`. See [key permissions](../../../../documentation/model-apis/authentication/key-based.md#permissions).



## OpenAPI

````yaml /api-reference/platform-apis/openapi/v1.json get /compute/instances
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
  /compute/instances:
    get:
      tags:
        - Compute
      summary: List Compute Instances
      description: >-
        Returns a list of all compute instances belonging to the authenticated
        user's workspace.


        **Requirements:**

        - Requires compute permissions (extra_permissions.compute = true)


        **Key Features:**

        - View all instances regardless of status

        - Includes instance configuration, region, and current status

        - Paginated results for large instance lists


        **Common Use Cases:**

        - Monitor all active compute resources

        - Check instance status and availability

        - Audit compute resource usage

        - Build compute management dashboards


        See [fal.ai docs](https://fal.ai/docs/documentation/compute) for more
        details.


        **Required permissions:** `compute:instances:read`. Key presets that
        include them: `COMPUTE`, `READONLY`, `FULL`. See [key
        permissions](https://fal.ai/docs/documentation/model-apis/authentication/key-based#permissions).
      operationId: listComputeInstances
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
      responses:
        '200':
          description: Successfully retrieved compute instances
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
                  instances:
                    type: array
                    items:
                      type: object
                      properties:
                        id:
                          type: string
                          description: Unique identifier for the compute instance
                          example: inst_abc123xyz
                        instance_type:
                          type: string
                          enum:
                            - gpu_8x_h100_sxm5
                            - gpu_1x_h100_sxm5
                          description: Type of compute instance (GPU configuration)
                          example: gpu_1x_h100_sxm5
                        region:
                          type: string
                          enum:
                            - us-west
                            - us-central
                            - us-east
                            - eu-north
                            - eu-west
                            - other
                          description: Geographical region where the instance is located
                          example: us-west
                        sector:
                          type: string
                          enum:
                            - sector_1
                            - sector_2
                            - sector_3
                          description: >-
                            Sector identifier for instance placement within the
                            region (if applicable)
                          example: sector_1
                        ip:
                          type: string
                          description: >-
                            IP address of the instance (available when instance
                            is ready)
                          example: 203.0.113.42
                        status:
                          type: string
                          enum:
                            - ready
                            - init
                            - pending
                            - provisioning
                            - stopped
                            - unknown
                          description: Current operational status of the instance
                          example: ready
                        creator_user_nickname:
                          type: string
                          description: Nickname of the user who created this instance
                          example: developer
                      required:
                        - id
                        - instance_type
                        - region
                        - status
                      description: >-
                        Compute instance details including configuration,
                        location, and status
                    description: >-
                      Array of compute instances belonging to the authenticated
                      user
                required:
                  - next_cursor
                  - has_more
                  - instances
                description: >-
                  Response containing a list of compute instances with
                  pagination support
              example:
                instances:
                  - id: inst_abc123xyz
                    instance_type: gpu_1x_h100_sxm5
                    region: us-west
                    sector: sector_1
                    ip: 203.0.113.42
                    status: ready
                    creator_user_nickname: developer
                  - id: inst_def456uvw
                    instance_type: gpu_8x_h100_sxm5
                    region: us-east
                    status: provisioning
                    creator_user_nickname: developer
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
