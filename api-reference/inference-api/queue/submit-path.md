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

# Submit a request to an endpoint path

> Add a request for a model endpoint path to the queue. Use this operation when the model ID has more than two segments, for example `blackforestlabs/flux-3-max/text-to-video`. The response contains the request ID and the URLs for status, result, and cancellation. The queue keeps the request until a runner is available. The queue has no size limit unless you send `fal_max_queue_length`.



## OpenAPI

````yaml /api-reference/inference-api/openapi/queue.json post /{owner}/{app}/{path}
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
  /{owner}/{app}/{path}:
    post:
      tags:
        - Queue
      summary: Submit a request to an endpoint path
      description: >-
        Add a request for a model endpoint path to the queue. Use this operation
        when the model ID has more than two segments, for example
        `blackforestlabs/flux-3-max/text-to-video`. The response contains the
        request ID and the URLs for status, result, and cancellation. The queue
        keeps the request until a runner is available. The queue has no size
        limit unless you send `fal_max_queue_length`.
      operationId: submitQueueRequestWithPath
      parameters:
        - $ref: '#/components/parameters/Owner'
        - $ref: '#/components/parameters/App'
        - $ref: '#/components/parameters/EndpointPath'
        - $ref: '#/components/parameters/FalWebhook'
        - $ref: '#/components/parameters/FalMaxQueueLength'
        - $ref: '#/components/parameters/StartTimeout'
        - $ref: '#/components/parameters/LegacyTimeout'
        - $ref: '#/components/parameters/QueuePriority'
        - $ref: '#/components/parameters/RunnerHint'
        - $ref: '#/components/parameters/NoRetry'
        - $ref: '#/components/parameters/RetryConfig'
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
          description: The request is in the queue.
          headers:
            x-fal-request-id:
              $ref: '#/components/headers/RequestId'
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/SubmitResponse'
        '400':
          description: A header or query parameter is not valid.
          content:
            application/json:
              schema:
                oneOf:
                  - $ref: '#/components/schemas/DetailError'
                  - $ref: '#/components/schemas/QueueSubmitError'
        '401':
          $ref: '#/components/responses/BadCredentials'
        '403':
          $ref: '#/components/responses/Forbidden'
        '404':
          $ref: '#/components/responses/AppNotFound'
        '413':
          $ref: '#/components/responses/PayloadTooLarge'
        '429':
          description: The queue has `fal_max_queue_length` requests or more.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/QueueSubmitError'
        '500':
          $ref: '#/components/responses/InternalError'
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
    FalWebhook:
      name: fal_webhook
      in: query
      required: false
      description: >-
        An `http` or `https` URL. fal sends a `POST` request to this URL when
        the request completes. See
        [Webhooks](https://fal.ai/docs/documentation/model-apis/inference/webhooks).
      schema:
        type: string
        format: uri
      example: https://example.com/fal-webhook
    FalMaxQueueLength:
      name: fal_max_queue_length
      in: query
      required: false
      description: >-
        The maximum number of queued requests. If the queue for this endpoint
        already has this number of requests, the API rejects the request with
        `429`.
      schema:
        type: integer
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
    QueuePriority:
      name: X-Fal-Queue-Priority
      in: header
      required: false
      description: >-
        The queue priority for this request. The default is `normal`. Use `low`
        for background work. This header can only lower the priority of your
        account. It cannot raise it. The API ignores values that it does not
        recognize.
      schema:
        type: string
        enum:
          - normal
          - low
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
    NoRetry:
      name: X-Fal-No-Retry
      in: header
      required: false
      description: >-
        Set to `1`, `true`, or `yes` to turn off automatic retries for this
        queue request. See
        [Reliability](https://fal.ai/docs/documentation/model-apis/inference/reliability).
      schema:
        type: string
        enum:
          - '1'
          - 'true'
          - 'yes'
    RetryConfig:
      name: X-Fal-Retry-Config
      in: header
      required: false
      description: >-
        A JSON object that sets the number of retries for each retry condition.
        The keys are `timeout`, `connection_error`, and `server_error`. Each
        value is `{"retries": N}`, where N is from 0 to 10. The API uses this
        header only when you own the app.
      schema:
        type: string
      example: '{"server_error": {"retries": 2}}'
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
    SubmitResponse:
      allOf:
        - $ref: '#/components/schemas/QueueStatus'
      example:
        status: IN_QUEUE
        request_id: 019a1b2c-3d4e-7f80-9a1b-2c3d4e5f6a7b
        response_url: >-
          https://queue.fal.run/blackforestlabs/flux-3-max/requests/019a1b2c-3d4e-7f80-9a1b-2c3d4e5f6a7b
        status_url: >-
          https://queue.fal.run/blackforestlabs/flux-3-max/requests/019a1b2c-3d4e-7f80-9a1b-2c3d4e5f6a7b/status
        cancel_url: >-
          https://queue.fal.run/blackforestlabs/flux-3-max/requests/019a1b2c-3d4e-7f80-9a1b-2c3d4e5f6a7b/cancel
        queue_position: 0
        logs: null
        metrics: {}
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
    QueueSubmitError:
      type: object
      description: A queue submit error for a query parameter.
      properties:
        request_id:
          type: 'null'
        error:
          type: string
      required:
        - error
    QueueStatus:
      type: object
      description: The state of a queue request.
      properties:
        status:
          type: string
          enum:
            - IN_QUEUE
            - IN_PROGRESS
            - COMPLETED
          description: >-
            `COMPLETED` is the final state. A failed or cancelled request also
            has the status `COMPLETED`.
        request_id:
          type: string
        response_url:
          type:
            - string
            - 'null'
          format: uri
          description: The URL of the result.
        status_url:
          type:
            - string
            - 'null'
          format: uri
        cancel_url:
          type:
            - string
            - 'null'
          format: uri
        queue_position:
          type: integer
          description: >-
            The number of requests in front of this request. Present only when
            the status is `IN_QUEUE`.
        logs:
          type:
            - array
            - 'null'
          items:
            $ref: '#/components/schemas/LogEntry'
          description: The runner logs. The value is `null` unless you send `logs=1`.
        metrics:
          type: object
          properties:
            inference_time:
              type: number
              description: The run time on the runner, in seconds.
          description: Present when the status is `COMPLETED`. Empty in other states.
        error:
          type: string
          description: The error message. Present only when the request failed.
        error_type:
          type: string
          description: >-
            The error category. Present only when the request failed. See
            [Request Error
            Types](https://fal.ai/docs/documentation/model-apis/request-errors).
      required:
        - status
        - request_id
    LogEntry:
      type: object
      properties:
        message:
          type: string
        timestamp:
          type: string
          format: date-time
        labels:
          type: object
          additionalProperties: true
      required:
        - message
        - timestamp
  headers:
    RequestId:
      description: The request ID. Include it when you contact support.
      schema:
        type: string
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
    InternalError:
      description: An internal error occurred. Retry the request.
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
