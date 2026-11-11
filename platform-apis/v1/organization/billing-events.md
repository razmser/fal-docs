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

# Organization Billing Events

> 
Returns paginated individual billing event records across all teams in your
organization, with each record attributed to a specific team via the
`username` field. Each record includes a per-request cost breakdown in USD
(cost_subtotal, cost_discount, cost_total; cost_estimate_nano_usd carries
cost_total in nano USD).

Scoped to fal Model API billing events. Must be called with a key on the organization's
root team.

> **Availability:** This endpoint is available to enterprise customers with organizations enabled. Contact your account team or support@fal.ai to request access.

**Key Features:**
- Organization-wide model-API billing event records across all teams
- Filter by team(s) (`team_username`), endpoint, API key (`api_key_id`), request ID, and date range
- Per-team attribution and per-request cost breakdown on every record
- Cursor-based pagination for efficient large dataset queries
- Limited to 100 records per page for performance
- Date range capped at 90 days per request

See [fal.ai docs](../../../documentation/model-apis/faq.md) for more details.
    

**Required permissions:** `account:users:read`, `billing:usage:read`, `auth:keys:read`. Key presets that include them: `BILLING`, `FULL`. See [key permissions](../../../documentation/model-apis/authentication/key-based.md#permissions).



## OpenAPI

````yaml /api-reference/platform-apis/openapi/v1.json get /organization/billing-events
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
  /organization/billing-events:
    get:
      tags:
        - Organization
      summary: Organization Billing Events
      description: >-

        Returns paginated individual billing event records across all teams in
        your

        organization, with each record attributed to a specific team via the

        `username` field. Each record includes a per-request cost breakdown in
        USD

        (cost_subtotal, cost_discount, cost_total; cost_estimate_nano_usd
        carries

        cost_total in nano USD).


        Scoped to fal Model API billing events. Must be called with a key on the
        organization's

        root team.


        > **Availability:** This endpoint is available to enterprise customers
        with organizations enabled. Contact your account team or support@fal.ai
        to request access.


        **Key Features:**

        - Organization-wide model-API billing event records across all teams

        - Filter by team(s) (`team_username`), endpoint, API key (`api_key_id`),
        request ID, and date range

        - Per-team attribution and per-request cost breakdown on every record

        - Cursor-based pagination for efficient large dataset queries

        - Limited to 100 records per page for performance

        - Date range capped at 90 days per request


        See [fal.ai docs](https://fal.ai/docs/documentation/model-apis/faq) for
        more details.
            

        **Required permissions:** `account:users:read`, `billing:usage:read`,
        `auth:keys:read`. Key presets that include them: `BILLING`, `FULL`. See
        [key
        permissions](https://fal.ai/docs/documentation/model-apis/authentication/key-based#permissions).
      operationId: getOrganizationBillingEvents
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
            anyOf:
              - type: string
                format: date-time
              - type: string
                pattern: ^\d{4}-\d{2}-\d{2}$
            description: >-
              Start date in ISO8601 format (e.g., '2025-01-01T00:00:00Z' or
              '2025-01-01'). Defaults to 24 hours ago.
            example: '2025-01-01T00:00:00Z'
          required: false
          description: >-
            Start date in ISO8601 format (e.g., '2025-01-01T00:00:00Z' or
            '2025-01-01'). Defaults to 24 hours ago.
          name: start
          in: query
        - schema:
            anyOf:
              - type: string
                format: date-time
              - type: string
                pattern: ^\d{4}-\d{2}-\d{2}$
            description: >-
              End date in ISO8601 format, exclusive (e.g.,
              '2025-02-01T00:00:00Z' or '2025-02-01'). Data up to but not
              including this timestamp is returned. Defaults to current time.
            example: '2025-02-01T00:00:00Z'
          required: false
          description: >-
            End date in ISO8601 format, exclusive (e.g., '2025-02-01T00:00:00Z'
            or '2025-02-01'). Data up to but not including this timestamp is
            returned. Defaults to current time.
          name: end
          in: query
        - schema:
            anyOf:
              - type: string
              - type: array
                items:
                  type: string
            description: >-
              Filter by specific endpoint ID(s). Accepts 1-50 endpoint IDs.
              Supports comma-separated values: ?endpoint_id=model1,model2 or
              array syntax: ?endpoint_id=model1&endpoint_id=model2
            example:
              - fal-ai/flux/dev
          required: false
          description: >-
            Filter by specific endpoint ID(s). Accepts 1-50 endpoint IDs.
            Supports comma-separated values: ?endpoint_id=model1,model2 or array
            syntax: ?endpoint_id=model1&endpoint_id=model2
          name: endpoint_id
          style: form
          explode: true
          in: query
        - schema:
            anyOf:
              - type: string
              - type: array
                items:
                  type: string
            description: >-
              Filter by specific request ID(s). Accepts 1-50 request IDs.
              Supports comma-separated values: ?request_id=req1,req2 or array
              syntax: ?request_id=req1&request_id=req2
            example:
              - req-abc123
          required: false
          description: >-
            Filter by specific request ID(s). Accepts 1-50 request IDs. Supports
            comma-separated values: ?request_id=req1,req2 or array syntax:
            ?request_id=req1&request_id=req2
          name: request_id
          style: form
          explode: true
          in: query
        - schema:
            anyOf:
              - type: string
              - type: array
                items:
                  type: string
            description: >-
              Filter by specific API key ID(s). Accepts 1-50 key IDs. Supports
              comma-separated values: ?api_key_id=key1,key2 or array syntax:
              ?api_key_id=key1&api_key_id=key2
            example:
              - abc123
          required: false
          description: >-
            Filter by specific API key ID(s). Accepts 1-50 key IDs. Supports
            comma-separated values: ?api_key_id=key1,key2 or array syntax:
            ?api_key_id=key1&api_key_id=key2
          name: api_key_id
          style: form
          explode: true
          in: query
        - schema:
            anyOf:
              - type: string
              - type: array
                items:
                  type: string
            description: >-
              Filter billing events by one or more team usernames within the
              organization. Accepts a comma-separated list or repeated
              parameter. If not provided, returns billing events across all
              teams.
            example:
              - acme-ml-team
          required: false
          description: >-
            Filter billing events by one or more team usernames within the
            organization. Accepts a comma-separated list or repeated parameter.
            If not provided, returns billing events across all teams.
          name: team_username
          style: form
          explode: true
          in: query
        - schema:
            anyOf:
              - type: string
              - type: array
                items:
                  type: string
            description: >-
              Data to include in the response. Use 'auth_method' for a resolved
              authentication method label, and 'auth_method_structured' for a
              machine-readable auth method object (detail, api_key_id,
              login_username).
            example: auth_method
          required: false
          description: >-
            Data to include in the response. Use 'auth_method' for a resolved
            authentication method label, and 'auth_method_structured' for a
            machine-readable auth method object (detail, api_key_id,
            login_username).
          name: expand
          style: form
          explode: true
          in: query
      responses:
        '200':
          description: Organization billing event data retrieved successfully
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
                  billing_events:
                    type: array
                    items:
                      type: object
                      properties:
                        username:
                          type: string
                          description: Team username that generated this billing event
                        request_id:
                          type: string
                          description: Unique identifier for the request
                        endpoint_id:
                          type: string
                          description: >-
                            Endpoint identifier that was used (e.g.,
                            'fal-ai/flux/dev')
                        timestamp:
                          type: string
                          description: Request timestamp in ISO8601 format
                        quantity:
                          type:
                            - number
                            - 'null'
                          minimum: 0
                          description: >-
                            Billable units consumed, in the unit named by
                            `unit`. Same value as the deprecated `output_units`.
                        output_units:
                          type:
                            - number
                            - 'null'
                          minimum: 0
                          description: 'Deprecated: use quantity. Same value as quantity.'
                          deprecated: true
                        unit:
                          type:
                            - string
                            - 'null'
                          description: >-
                            The billing unit these units are counted in (e.g.
                            'image', 'second', 'megapixel'). Null when the
                            reporter recorded none.
                        unit_price:
                          type:
                            - number
                            - 'null'
                          minimum: 0
                          description: >-
                            Per-unit price this line is measured against: the
                            list rate when a discount is reported in
                            percent_discount, otherwise the negotiated rate
                            after any price override and before percentage
                            discounts.
                        percent_discount:
                          type:
                            - number
                            - 'null'
                          description: >-
                            Discount percentage applied to this request (e.g.,
                            10 = 10% discount)
                        cost_subtotal:
                          type: number
                          minimum: 0
                          description: >-
                            Cost before discounts in USD (output_units ×
                            unit_price)
                        cost_discount:
                          type: number
                          minimum: 0
                          description: >-
                            Discount applied to this request in USD
                            (cost_subtotal − cost_total)
                        cost_total:
                          type: number
                          minimum: 0
                          description: >-
                            Amount charged after discounts in USD (cost_subtotal
                            − cost_discount)
                        cost_estimate_nano_usd:
                          type: number
                          minimum: 0
                          description: >-
                            Amount charged after discounts in nano USD. The
                            precision-preserving representation of cost_total,
                            not a legacy duplicate: an integer nano-USD amount
                            avoids the float error that accumulates when summing
                            sub-cent charges (1 USD = 1,000,000,000 nano USD).
                        auth_method:
                          type: string
                          description: >-
                            Authentication method label resolved across the
                            organization (e.g., 'my-key (owner: acme-ml-team)',
                            'alice <alice@example.com>', or 'None'). Only
                            populated when 'auth_method' is included in the
                            expand parameter.
                        auth_method_structured:
                          type: object
                          properties:
                            detail:
                              type: string
                              description: >-
                                Human-readable summary of the authentication
                                method (key alias, login username, or 'User
                                token'/'None').
                            api_key_id:
                              type: string
                              description: >-
                                API key ID, present when the request was made
                                with an API key.
                            login_username:
                              type: string
                              description: >-
                                Team member login username (nickname), present
                                when the request was made with a user login
                                token resolvable to a team member.
                          required:
                            - detail
                          description: >-
                            Structured authentication method identity resolved
                            across the organization. Only populated when
                            'auth_method_structured' is included in the expand
                            parameter.
                      required:
                        - username
                        - request_id
                        - endpoint_id
                        - timestamp
                        - quantity
                        - output_units
                        - unit
                        - unit_price
                        - percent_discount
                        - cost_subtotal
                        - cost_discount
                        - cost_total
                        - cost_estimate_nano_usd
                      description: >-
                        Individual organization billing event record with team
                        attribution
                    description: List of billing event records with team attribution
                required:
                  - next_cursor
                  - has_more
                  - billing_events
                description: >-
                  Response containing organization billing event data with
                  pagination support
              example:
                billing_events:
                  - username: acme-ml-team
                    request_id: abc123
                    endpoint_id: fal-ai/flux/dev
                    timestamp: '2025-01-15T10:30:45Z'
                    quantity: 2
                    output_units: 2
                    unit: image
                    unit_price: 0.025
                    percent_discount: 10
                    cost_subtotal: 0.05
                    cost_discount: 0.005
                    cost_total: 0.045
                    cost_estimate_nano_usd: 45000000
                  - username: acme-research
                    request_id: def456
                    endpoint_id: fal-ai/flux/dev
                    timestamp: '2025-01-15T10:25:30Z'
                    quantity: 1.5
                    output_units: 1.5
                    unit: image
                    unit_price: 0.025
                    percent_discount: null
                    cost_subtotal: 0.0375
                    cost_discount: 0
                    cost_total: 0.0375
                    cost_estimate_nano_usd: 37500000
                next_cursor: MjAyNS0wMS0xNVQxMDoyNTozMFp8ZGVmNDU2
                has_more: true
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
        - billingApiKey: []
components:
  securitySchemes:
    billingApiKey:
      type: apiKey
      in: header
      name: Authorization
      description: >-
        Key on the `BILLING` preset, or any key whose permissions include the
        ones this API lists. Prefix it with "Key ", e.g. Authorization: Key
        YOUR_API_KEY

````
