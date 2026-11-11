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

# Get request result

> Get the result of a completed request. The response has the status code, headers, and body that the model returned. If the model returned an error, this operation returns the same error.

fal keeps the result for 1 hour after the request completes. If the result is 10 KB or larger, fal keeps it for 6 minutes. If payload storage is on, the result stays available for the storage period. See `X-Fal-Store-IO`.



## OpenAPI

````yaml /api-reference/inference-api/openapi/queue.json get /{owner}/{app}/requests/{request_id}
openapi: 3.1.0
info:
  title: Queue API
  version: v1
  description: >-
    Submit a model request to the fal queue, then read its status and result.
    The queue retries requests that fail because of a temporary runner error.
servers:
  - url: https://queue.fal.run
    description: Queue API. The backup host is https://queue.falrun.com.
security:
  - apiKey: []
tags:
  - name: Queue
paths:
  /{owner}/{app}/requests/{request_id}:
    get:
      tags:
        - Queue
      summary: Get request result
      description: >-
        Get the result of a completed request. The response has the status code,
        headers, and body that the model returned. If the model returned an
        error, this operation returns the same error.


        fal keeps the result for 1 hour after the request completes. If the
        result is 10 KB or larger, fal keeps it for 6 minutes. If payload
        storage is on, the result stays available for the storage period. See
        `X-Fal-Store-IO`.
      operationId: getQueueRequestResult
      parameters:
        - $ref: '#/components/parameters/StatusOwner'
        - $ref: '#/components/parameters/StatusApp'
        - $ref: '#/components/parameters/RequestId'
      responses:
        '200':
          description: The model output.
          headers:
            x-fal-request-id:
              $ref: '#/components/headers/RequestId'
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ModelOutput'
        '400':
          description: The request is not complete.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/InProgressError'
        '401':
          $ref: '#/components/responses/BadCredentials'
        '404':
          description: >-
            The request does not exist, the result expired, or the request
            belongs to a different account.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/DetailError'
        '422':
          description: The model rejected the input.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ModelValidationError'
        '499':
          description: The request was cancelled.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/DetailError'
        '500':
          description: >-
            The model or the platform failed. The body is the error that the
            model or the platform returned.
          content:
            application/json:
              schema:
                anyOf:
                  - $ref: '#/components/schemas/RequestError'
                  - $ref: '#/components/schemas/DetailError'
        '504':
          description: >-
            The request did not start before the `X-Fal-Request-Start-Timeout`
            deadline.
          headers:
            X-Fal-Request-Timeout-Type:
              $ref: '#/components/headers/TimeoutType'
            X-Fal-Error-Type:
              $ref: '#/components/headers/ErrorType'
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/RequestError'
components:
  parameters:
    StatusOwner:
      name: owner
      in: path
      required: true
      description: >-
        The owner of the model, for example `blackforestlabs`. Use the same
        value as in the submit request.
      schema:
        type: string
      example: blackforestlabs
    StatusApp:
      name: app
      in: path
      required: true
      description: >-
        The model app name, for example `flux-3-max`. Do not add the endpoint
        path. The `status_url`, `response_url`, and `cancel_url` fields contain
        the correct URL.
      schema:
        type: string
      example: flux-3-max
    RequestId:
      name: request_id
      in: path
      required: true
      description: The request ID that the submit response returned.
      schema:
        type: string
        format: uuid
  headers:
    RequestId:
      description: The request ID. Include it when you contact support.
      schema:
        type: string
    TimeoutType:
      description: >-
        `user` when the request did not start before the
        `X-Fal-Request-Start-Timeout` deadline.
      schema:
        type: string
        enum:
          - user
    ErrorType:
      description: >-
        The same value as the `error_type` field. See [Request Error
        Types](https://fal.ai/docs/documentation/model-apis/request-errors).
      schema:
        type: string
        enum:
          - request_timeout
          - startup_timeout
          - runner_scheduling_failure
          - runner_connection_timeout
          - runner_disconnected
          - runner_connection_refused
          - runner_connection_error
          - runner_incomplete_response
          - runner_server_error
          - client_disconnected
          - client_cancelled
          - bad_request
          - internal_error
  schemas:
    ModelOutput:
      type: object
      description: The model output. Each model has its own output schema.
      additionalProperties: true
    InProgressError:
      type: object
      properties:
        detail:
          type: string
          enum:
            - Request is still in progress
        request_id:
          type: string
        response_url:
          type: string
          format: uri
        status_url:
          type: string
          format: uri
        cancel_url:
          type: string
          format: uri
      required:
        - detail
    DetailError:
      type: object
      description: A general error.
      properties:
        detail:
          type: string
        type:
          type: string
          description: >-
            A machine-readable reason, for some errors. Examples are
            `rejected_key`, `permission_denied`, and `resource_restricted`.
      required:
        - detail
    ModelValidationError:
      type: object
      description: >-
        An input validation error from the model. See [Model
        Errors](https://fal.ai/docs/documentation/model-apis/errors).
      properties:
        detail:
          type: array
          items:
            type: object
            properties:
              loc:
                type: array
                items:
                  type:
                    - string
                    - integer
              msg:
                type: string
              type:
                type: string
              url:
                type: string
              ctx:
                type: object
              input: {}
            required:
              - loc
              - msg
              - type
      required:
        - detail
    RequestError:
      type: object
      description: >-
        An error from the request pipeline. See [Request Error
        Types](https://fal.ai/docs/documentation/model-apis/request-errors).
      properties:
        detail:
          type: string
        error_type:
          type: string
          enum:
            - request_timeout
            - startup_timeout
            - runner_scheduling_failure
            - runner_connection_timeout
            - runner_disconnected
            - runner_connection_refused
            - runner_connection_error
            - runner_incomplete_response
            - runner_server_error
            - client_disconnected
            - client_cancelled
            - bad_request
            - internal_error
      required:
        - detail
        - error_type
  responses:
    BadCredentials:
      description: The credentials are not valid, or the app requires authentication.
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/DetailError'
  securitySchemes:
    apiKey:
      type: apiKey
      in: header
      name: Authorization
      description: >-
        Send your fal API key in the `Authorization` header with the `Key`
        prefix, for example `Authorization: Key $FAL_KEY`. Public models also
        accept requests without a key. See
        [Authentication](https://fal.ai/docs/documentation/model-apis/authentication/key-based).

````
