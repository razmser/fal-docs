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

# Stream request status

> Receive status updates as server-sent events. Each event has a `data:` line with a status object. The status object has the same fields as the response of Get request status. The server closes the stream after it sends the `COMPLETED` event. The server sends a comment line every 15 seconds to keep the connection open.



## OpenAPI

````yaml /api-reference/inference-api/openapi/queue.json get /{owner}/{app}/requests/{request_id}/status/stream
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
  /{owner}/{app}/requests/{request_id}/status/stream:
    get:
      tags:
        - Queue
      summary: Stream request status
      description: >-
        Receive status updates as server-sent events. Each event has a `data:`
        line with a status object. The status object has the same fields as the
        response of Get request status. The server closes the stream after it
        sends the `COMPLETED` event. The server sends a comment line every 15
        seconds to keep the connection open.
      operationId: streamQueueRequestStatus
      parameters:
        - $ref: '#/components/parameters/StatusOwner'
        - $ref: '#/components/parameters/StatusApp'
        - $ref: '#/components/parameters/RequestId'
        - $ref: '#/components/parameters/Logs'
      responses:
        '200':
          description: A stream of status events.
          content:
            text/event-stream:
              schema:
                type: string
              example: >+
                data:
                {"status":"IN_PROGRESS","request_id":"019a1b2c-...","logs":null,"metrics":{}}


                data:
                {"status":"COMPLETED","request_id":"019a1b2c-...","logs":null,"metrics":{"inference_time":1.82}}

        '401':
          $ref: '#/components/responses/BadCredentials'
        '404':
          description: >-
            The request does not exist, or it belongs to a different account.
            The API returns the same response in both cases.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotFoundStatus'
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
    Logs:
      name: logs
      in: query
      required: false
      description: >-
        Set to `1` to include runner logs in the response. Logs are not
        available for all models.
      schema:
        type: string
        enum:
          - '1'
          - '0'
  responses:
    BadCredentials:
      description: The credentials are not valid, or the app requires authentication.
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/DetailError'
  schemas:
    NotFoundStatus:
      type: object
      properties:
        status:
          type: string
          enum:
            - NOT_FOUND
      required:
        - status
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
