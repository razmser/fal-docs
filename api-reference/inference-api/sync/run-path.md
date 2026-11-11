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

# Run an endpoint path

> Run a model endpoint path and wait for the result. Use this operation when the model ID has more than two segments, for example `blackforestlabs/flux-3-max/text-to-video`. For models with a streaming endpoint, use the path `stream` to receive server-sent events. fal does not retry synchronous requests. If a request fails because of a temporary error, the response has `X-Fal-Needs-Retry: 1`. For long requests, use the [Queue API](../queue/submit.md).



## OpenAPI

````yaml /api-reference/inference-api/openapi/sync.json post /{owner}/{app}/{path}
openapi: 3.1.0
info:
  title: Synchronous API
  version: v1
  description: >-
    Send a model request and wait for the result in the same HTTP connection.
    Synchronous requests do not use the queue, and fal does not retry them.
servers:
  - url: https://fal.run
    description: Synchronous API. The backup host is https://falrun.com.
security:
  - apiKey: []
tags:
  - name: Synchronous
paths:
  /{owner}/{app}/{path}:
    post:
      tags:
        - Synchronous
      summary: Run an endpoint path
      description: >-
        Run a model endpoint path and wait for the result. Use this operation
        when the model ID has more than two segments, for example
        `blackforestlabs/flux-3-max/text-to-video`. For models with a streaming
        endpoint, use the path `stream` to receive server-sent events. fal does
        not retry synchronous requests. If a request fails because of a
        temporary error, the response has `X-Fal-Needs-Retry: 1`. For long
        requests, use the [Queue
        API](https://fal.ai/docs/api-reference/inference-api/queue/submit).
      operationId: runWithPath
      parameters:
        - $ref: '#/components/parameters/Owner'
        - $ref: '#/components/parameters/App'
        - $ref: '#/components/parameters/EndpointPath'
        - $ref: '#/components/parameters/StartTimeout'
        - $ref: '#/components/parameters/LegacyTimeout'
        - $ref: '#/components/parameters/RunnerHint'
        - $ref: '#/components/parameters/StoreIO'
        - $ref: '#/components/parameters/ObjectLifecycle'
        - $ref: '#/components/parameters/DisableFallback'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ModelInput'
      responses:
        '200':
          description: The model output.
          headers:
            x-fal-request-id:
              $ref: '#/components/headers/RequestId'
            X-Fal-Served-From:
              $ref: '#/components/headers/ServedFrom'
            X-Fal-Billable-Units:
              $ref: '#/components/headers/BillableUnits'
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ModelOutput'
            text/event-stream:
              schema:
                type: string
                description: >-
                  Server-sent events. Returned when the path is a streaming
                  endpoint, for example `stream`.
        '400':
          description: A header is not valid, for example `X-Fal-Request-Start-Timeout`.
          headers:
            x-fal-request-id:
              $ref: '#/components/headers/RequestId'
            X-Fal-Error-Type:
              $ref: '#/components/headers/ErrorType'
            X-Fal-Needs-Retry:
              $ref: '#/components/headers/NeedsRetry'
            X-Fal-Needs-Retry-Type:
              $ref: '#/components/headers/NeedsRetryType'
          content:
            application/json:
              schema:
                anyOf:
                  - $ref: '#/components/schemas/RequestError'
                  - $ref: '#/components/schemas/DetailError'
        '401':
          $ref: '#/components/responses/BadCredentials'
        '403':
          $ref: '#/components/responses/Forbidden'
        '404':
          $ref: '#/components/responses/AppNotFound'
        '413':
          $ref: '#/components/responses/PayloadTooLarge'
        '422':
          description: The model rejected the input.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ModelValidationError'
        '429':
          description: >-
            Your account has the maximum number of concurrent requests. Retry
            later, or use the queue. The queue waits for capacity.
          headers:
            x-fal-request-id:
              $ref: '#/components/headers/RequestId'
            X-Fal-Needs-Retry:
              $ref: '#/components/headers/NeedsRetry'
            X-Fal-Needs-Retry-Type:
              $ref: '#/components/headers/NeedsRetryType'
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ConcurrencyLimitError'
        '500':
          description: The model or the platform failed.
          headers:
            x-fal-request-id:
              $ref: '#/components/headers/RequestId'
            X-Fal-Error-Type:
              $ref: '#/components/headers/ErrorType'
            X-Fal-Needs-Retry:
              $ref: '#/components/headers/NeedsRetry'
            X-Fal-Needs-Retry-Type:
              $ref: '#/components/headers/NeedsRetryType'
          content:
            application/json:
              schema:
                anyOf:
                  - $ref: '#/components/schemas/RequestError'
                  - $ref: '#/components/schemas/DetailError'
        '503':
          description: >-
            fal could not get a runner or connect to it. The `error_type` field
            gives the reason. A retry can succeed.
          headers:
            x-fal-request-id:
              $ref: '#/components/headers/RequestId'
            X-Fal-Error-Type:
              $ref: '#/components/headers/ErrorType'
            X-Fal-Needs-Retry:
              $ref: '#/components/headers/NeedsRetry'
            X-Fal-Needs-Retry-Type:
              $ref: '#/components/headers/NeedsRetryType'
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/RequestError'
        '504':
          description: >-
            The request timed out. `startup_timeout` means that the request did
            not start before the `X-Fal-Request-Start-Timeout` deadline.
            `request_timeout` means that the runner did not respond in the time
            limit of the app.
          headers:
            x-fal-request-id:
              $ref: '#/components/headers/RequestId'
            X-Fal-Error-Type:
              $ref: '#/components/headers/ErrorType'
            X-Fal-Needs-Retry:
              $ref: '#/components/headers/NeedsRetry'
            X-Fal-Needs-Retry-Type:
              $ref: '#/components/headers/NeedsRetryType'
            X-Fal-Request-Timeout-Type:
              $ref: '#/components/headers/TimeoutType'
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/RequestError'
components:
  parameters:
    Owner:
      name: owner
      in: path
      required: true
      description: The owner of the model, for example `blackforestlabs`.
      schema:
        type: string
      example: blackforestlabs
    App:
      name: app
      in: path
      required: true
      description: The model app name, for example `flux-3-max`.
      schema:
        type: string
      example: flux-3-max
    EndpointPath:
      name: path
      in: path
      required: true
      description: >-
        The endpoint path inside the app. For the model ID
        `blackforestlabs/flux-3-max/text-to-video`, the owner is
        `blackforestlabs`, the app is `flux-3-max`, and the path is
        `text-to-video`. The path can contain more than one segment.
      schema:
        type: string
      example: text-to-video
    StartTimeout:
      name: X-Fal-Request-Start-Timeout
      in: header
      required: false
      description: >-
        The maximum time in seconds for the request to start on a runner. The
        time starts when fal receives the request and includes queue time and
        retries. The value must be greater than `0.1`. If the request does not
        start in this time, the API returns `504` with
        `X-Fal-Request-Timeout-Type: user`. This header does not limit the run
        time on the runner. There is no default.
      schema:
        type: number
        exclusiveMinimum: 0.1
      example: '30'
    LegacyTimeout:
      name: X-Fal-Request-Timeout
      in: header
      required: false
      description: >-
        Deprecated. Use `X-Fal-Request-Start-Timeout`. If you send both headers,
        the API uses `X-Fal-Request-Start-Timeout`.
      schema:
        type: number
        exclusiveMinimum: 0.1
      deprecated: true
    RunnerHint:
      name: X-Fal-Runner-Hint
      in: header
      required: false
      description: >-
        A routing hint. fal tries to send requests with the same hint to the
        same runner. Use it when a runner keeps state in memory, for example a
        loaded LoRA.
      schema:
        type: string
      example: lora:my-style
    StoreIO:
      name: X-Fal-Store-IO
      in: header
      required: false
      description: >-
        Controls if fal stores the request and response payloads. Set to `0` to
        turn off storage. Set to `1` to store the payloads for the default
        period. If you do not send this header, fal uses your account setting.
      schema:
        type: string
      example: '0'
    ObjectLifecycle:
      name: X-Fal-Object-Lifecycle-Preference
      in: header
      required: false
      description: >-
        A JSON object that sets the expiration and access rules for the files
        that the model creates. See [Platform
        Headers](https://fal.ai/docs/documentation/model-apis/common-parameters).
      schema:
        type: string
      example: '{"expiration_duration_seconds": 86400}'
    DisableFallback:
      name: x-app-fal-disable-fallback
      in: header
      required: false
      description: >-
        Set to `true` to ask the model app not to send the request to a fallback
        model. fal forwards this header to the app.
      schema:
        type: string
        enum:
          - 'true'
  schemas:
    ModelInput:
      type: object
      description: >-
        The model input. Each model has its own input schema. See the model page
        in the [Model API Reference](https://fal.ai/docs/model-api-reference).
      additionalProperties: true
      example:
        prompt: a red fox in the snow
    ModelOutput:
      type: object
      description: The model output. Each model has its own output schema.
      additionalProperties: true
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
    ConcurrencyLimitError:
      type: object
      properties:
        detail:
          type: string
          example: Reached concurrent requests limit of 2
        type:
          type: string
          enum:
            - concurrent_requests_limit
        data:
          type: object
          properties:
            limit:
              type: integer
            notify:
              type: boolean
      required:
        - detail
        - type
  headers:
    RequestId:
      description: The request ID. Include it when you contact support.
      schema:
        type: string
    ServedFrom:
      description: The ID of the runner that processed the request.
      schema:
        type: string
    BillableUnits:
      description: >-
        The number of billable units for the request, when the model reports
        them.
      schema:
        type: string
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
    NeedsRetry:
      description: >-
        `1` when the error is temporary and a retry can succeed. `0` when a
        retry cannot succeed.
      schema:
        type: string
        enum:
          - '0'
          - '1'
    NeedsRetryType:
      description: The retry condition that applies to the error.
      schema:
        type: string
        enum:
          - timeout
          - connection_error
          - server_error
          - internal_worker_error
          - concurrent_requests_limit
    TimeoutType:
      description: >-
        `user` when the request did not start before the
        `X-Fal-Request-Start-Timeout` deadline.
      schema:
        type: string
        enum:
          - user
  responses:
    BadCredentials:
      description: The credentials are not valid, or the app requires authentication.
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/DetailError'
    Forbidden:
      description: >-
        The key cannot use this model. Reasons include missing permissions, a
        model restriction on the key, a locked account, or a spending limit.
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/DetailError'
    AppNotFound:
      description: The model does not exist.
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/DetailError'
    PayloadTooLarge:
      description: The request body is larger than 64 MiB.
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
