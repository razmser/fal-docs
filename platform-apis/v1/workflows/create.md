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

# Create a workflow

> 
Create a new workflow owned by the authenticated user.

**Authentication:** Required.

**Common Use Cases:**
- Save a newly built workflow
- Programmatically provision workflows

**Note:** Workflow names must be unique within your namespace. Creating a
workflow with a name you already use returns a 400 validation error.
    

**Required permissions:** `workflows:write`. Key presets that include them: `SERVERLESS`, `FULL`. See [key permissions](../../../documentation/model-apis/authentication/key-based.md#permissions).



## OpenAPI

````yaml /api-reference/platform-apis/openapi/v1.json post /workflows
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
    post:
      tags:
        - Workflows
      summary: Create a workflow
      description: >-

        Create a new workflow owned by the authenticated user.


        **Authentication:** Required.


        **Common Use Cases:**

        - Save a newly built workflow

        - Programmatically provision workflows


        **Note:** Workflow names must be unique within your namespace. Creating
        a

        workflow with a name you already use returns a 400 validation error.
            

        **Required permissions:** `workflows:write`. Key presets that include
        them: `SERVERLESS`, `FULL`. See [key
        permissions](https://fal.ai/docs/documentation/model-apis/authentication/key-based#permissions).
      operationId: createWorkflow
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                name:
                  type: string
                  maxLength: 128
                  pattern: ^[a-zA-Z0-9_-]+$
                  description: Unique workflow name/slug within the user's namespace
                  example: my-image-workflow
                title:
                  type: string
                  minLength: 1
                  maxLength: 256
                  description: Human-readable workflow title
                  example: My Image Generation Workflow
                contents:
                  type: object
                  properties:
                    name:
                      type: string
                      description: Internal name of the workflow definition
                      example: my-image-workflow
                    version:
                      type: string
                      description: Workflow definition format version
                      example: 1.0.0
                    nodes:
                      type: object
                      additionalProperties:
                        type: object
                        additionalProperties: {}
                      description: Workflow nodes keyed by node id
                    output:
                      type: object
                      additionalProperties:
                        type: string
                      description: Output field mappings keyed by output name
                    schema:
                      type: object
                      properties:
                        input:
                          type: object
                          additionalProperties: {}
                          description: Input fields schema
                        output:
                          type: object
                          additionalProperties: {}
                          description: Output fields schema
                      required:
                        - input
                        - output
                      description: Input/output schema for the workflow
                    metadata:
                      type: object
                      additionalProperties: {}
                      description: Optional workflow metadata
                  required:
                    - name
                    - version
                    - nodes
                    - output
                    - schema
                  description: The workflow definition/configuration object
                  example:
                    name: my-image-workflow
                    version: 1.0.0
                    nodes:
                      node_a1b2c3:
                        id: node_a1b2c3
                        type: run
                        app: fal-ai/flux/dev
                        depends: []
                        input:
                          prompt: $input.prompt
                        metadata:
                          position:
                            x: 300
                            'y': 100
                    output:
                      image: $node_a1b2c3.images.0.url
                    schema:
                      input:
                        prompt:
                          type: string
                      output:
                        image:
                          type: string
                is_public:
                  type: boolean
                  default: false
                  description: Whether the workflow is publicly visible
                  example: false
              required:
                - name
                - title
                - contents
              description: Request body for creating a new workflow
            example:
              name: my-image-workflow
              title: My Image Generation Workflow
              is_public: false
              contents:
                name: my-image-workflow
                version: 1.0.0
                nodes:
                  node_a1b2c3:
                    id: node_a1b2c3
                    type: run
                    app: fal-ai/flux/dev
                    depends: []
                    input:
                      prompt: $input.prompt
                    metadata:
                      position:
                        x: 300
                        'y': 100
                output:
                  image: $node_a1b2c3.images.0.url
                schema:
                  input:
                    prompt:
                      type: string
                  output:
                    image:
                      type: string
      responses:
        '201':
          description: Successfully created workflow
          content:
            application/json:
              schema:
                type: object
                properties:
                  workflow:
                    type: object
                    properties:
                      name:
                        type: string
                        description: Unique workflow name/slug within the user's namespace
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
                      is_public:
                        type: boolean
                        description: Whether the workflow is publicly visible
                        example: true
                      contents:
                        type: object
                        additionalProperties: {}
                        description: The workflow definition/configuration object
                        example:
                          nodes:
                            node_a1b2c3:
                              id: node_a1b2c3
                              type: model
                              app: fal-ai/flux/dev
                              depends: []
                              input:
                                prompt: $input.prompt
                              metadata:
                                position:
                                  x: 300
                                  'y': 100
                            output:
                              id: output
                              type: output
                              depends:
                                - node_a1b2c3
                              fields:
                                image: $node_a1b2c3.images.0.url
                              metadata:
                                position:
                                  x: 600
                                  'y': 100
                    required:
                      - name
                      - title
                      - user_nickname
                      - created_at
                      - is_public
                      - contents
                    description: The workflow details
                    example:
                      name: my-image-workflow
                      title: My Image Generation Workflow
                      user_nickname: johndoe
                      created_at: '2024-01-15T10:30:00Z'
                      is_public: true
                      contents:
                        nodes:
                          node_a1b2c3:
                            id: node_a1b2c3
                            type: model
                            app: fal-ai/flux/dev
                            depends: []
                            input:
                              prompt: $input.prompt
                            metadata:
                              position:
                                x: 300
                                'y': 100
                          output:
                            id: output
                            type: output
                            depends:
                              - node_a1b2c3
                            fields:
                              image: $node_a1b2c3.images.0.url
                            metadata:
                              position:
                                x: 600
                                'y': 100
                required:
                  - workflow
                description: Response containing a single workflow's details
              example:
                workflow:
                  name: my-image-workflow
                  title: My Image Generation Workflow
                  user_nickname: johndoe
                  created_at: '2025-01-15T12:00:00Z'
                  is_public: false
                  contents:
                    name: my-image-workflow
                    version: 1.0.0
                    nodes:
                      node_a1b2c3:
                        id: node_a1b2c3
                        type: run
                        app: fal-ai/flux/dev
                        depends: []
                        input:
                          prompt: $input.prompt
                        metadata:
                          position:
                            x: 300
                            'y': 100
                    output:
                      image: $node_a1b2c3.images.0.url
                    schema:
                      input:
                        prompt:
                          type: string
                      output:
                        image:
                          type: string
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
        - serverlessApiKey: []
components:
  securitySchemes:
    serverlessApiKey:
      type: apiKey
      in: header
      name: Authorization
      description: >-
        Key on the `SERVERLESS` preset, or any key whose permissions include the
        ones this API lists. Prefix it with "Key ", e.g. Authorization: Key
        YOUR_API_KEY

````
