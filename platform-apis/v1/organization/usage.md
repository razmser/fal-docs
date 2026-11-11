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

# Organization Usage

> 
Returns paginated usage records across all teams and product lines in your
organization, with each record attributed to a specific team via the
`username` field and a product line via the `product` field.

Covers all three fal product lines:
- `model_apis` — model API endpoint calls (e.g. `fal-ai/flux/dev`)
- `serverless` — fal Serverless SDK billing
- `compute` — fal Compute (raw instance time)

> **Availability:** This endpoint is available to enterprise customers with organizations enabled. Contact your account team or support@fal.ai to request access.

Must be called with a key on the organization's root team.

**Key Features:**
- Organization-wide usage data across all teams and products
- Filter by team(s) (`team_username`), product line (`product`), endpoint, API key (`api_key_id`), date range, and auth method
- Per-team and per-product attribution on every usage record
- Paginated time series and aggregate summary views

See [fal.ai docs](../../../documentation.md) for more details.
    

**Required permissions:** `account:users:read`, `billing:usage:read`, `auth:keys:read`. Key presets that include them: `BILLING`, `FULL`. See [key permissions](../../../documentation/model-apis/authentication/key-based.md#permissions).



## OpenAPI

````yaml /api-reference/platform-apis/openapi/v1.json get /organization/usage
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
  /organization/usage:
    get:
      tags:
        - Organization
      summary: Organization Usage
      description: >-

        Returns paginated usage records across all teams and product lines in
        your

        organization, with each record attributed to a specific team via the

        `username` field and a product line via the `product` field.


        Covers all three fal product lines:

        - `model_apis` — model API endpoint calls (e.g. `fal-ai/flux/dev`)

        - `serverless` — fal Serverless SDK billing

        - `compute` — fal Compute (raw instance time)


        > **Availability:** This endpoint is available to enterprise customers
        with organizations enabled. Contact your account team or support@fal.ai
        to request access.


        Must be called with a key on the organization's root team.


        **Key Features:**

        - Organization-wide usage data across all teams and products

        - Filter by team(s) (`team_username`), product line (`product`),
        endpoint, API key (`api_key_id`), date range, and auth method

        - Per-team and per-product attribution on every usage record

        - Paginated time series and aggregate summary views


        See [fal.ai docs](https://fal.ai/docs/documentation) for more details.
            

        **Required permissions:** `account:users:read`, `billing:usage:read`,
        `auth:keys:read`. Key presets that include them: `BILLING`, `FULL`. See
        [key
        permissions](https://fal.ai/docs/documentation/model-apis/authentication/key-based#permissions).
      operationId: getOrganizationUsage
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
            type: string
            default: UTC
            description: >-
              Timezone for date aggregation and boundaries. All timestamps in
              responses are in UTC, but this controls how dates are bucketed.
            example: UTC
          required: false
          description: >-
            Timezone for date aggregation and boundaries. All timestamps in
            responses are in UTC, but this controls how dates are bucketed.
          name: timezone
          in: query
        - schema:
            type: string
            enum:
              - minute
              - hour
              - day
              - week
              - month
            description: >-
              Aggregation timeframe for timeseries data (auto-detected from date
              range if not specified). Auto-detection uses: minute (<2h), hour
              (<2d), day (<64d), week (<183d), month (>=183d).
            example: day
          required: false
          description: >-
            Aggregation timeframe for timeseries data (auto-detected from date
            range if not specified). Auto-detection uses: minute (<2h), hour
            (<2d), day (<64d), week (<183d), month (>=183d).
          name: timeframe
          in: query
        - schema:
            type: string
            enum:
              - 'true'
              - 'false'
            default: 'true'
            description: >-
              Whether to adjust start/end dates to align with timeframe
              boundaries and use exclusive end. Defaults to true. When true,
              dates are aligned to the start of the timeframe period (e.g.,
              start of day) and end is made exclusive (e.g., start of next day).
              When false, uses exact dates provided.
            example: 'true'
          required: false
          description: >-
            Whether to adjust start/end dates to align with timeframe boundaries
            and use exclusive end. Defaults to true. When true, dates are
            aligned to the start of the timeframe period (e.g., start of day)
            and end is made exclusive (e.g., start of next day). When false,
            uses exact dates provided.
          name: bound_to_timeframe
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
              Filter by one or more team usernames within the organization.
              Accepts a comma-separated list or repeated parameter. If not
              provided, returns usage across all teams.
            example:
              - acme-ml-team
          required: false
          description: >-
            Filter by one or more team usernames within the organization.
            Accepts a comma-separated list or repeated parameter. If not
            provided, returns usage across all teams.
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
              Restrict results to one or more product lines. Accepts a
              comma-separated list or repeated parameter. Defaults to all three
              (model_apis, serverless, compute).
            example:
              - model_apis
              - compute
          required: false
          description: >-
            Restrict results to one or more product lines. Accepts a
            comma-separated list or repeated parameter. Defaults to all three
            (model_apis, serverless, compute).
          name: product
          style: form
          explode: true
          in: query
        - schema:
            anyOf:
              - type: string
              - type: array
                items:
                  type: string
            default:
              - time_series
            description: >-
              Data to include in the response. Use 'time_series' for
              time-bucketed data, 'summary' for aggregate statistics,
              'auth_method' for a resolved authentication method label, and
              'auth_method_structured' for a machine-readable auth method object
              (detail, api_key_id, login_username). At least one of
              'time_series' or 'summary' is required.
            example:
              - time_series
              - auth_method
          required: false
          description: >-
            Data to include in the response. Use 'time_series' for time-bucketed
            data, 'summary' for aggregate statistics, 'auth_method' for a
            resolved authentication method label, and 'auth_method_structured'
            for a machine-readable auth method object (detail, api_key_id,
            login_username). At least one of 'time_series' or 'summary' is
            required.
          name: expand
          style: form
          explode: true
          in: query
      responses:
        '200':
          description: Organization usage data retrieved successfully
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
                  time_series:
                    type: array
                    items:
                      type: object
                      properties:
                        bucket:
                          type: string
                          description: >-
                            Time bucket timestamp in user's timezone with offset
                            (ISO8601 datetime)
                          example: '2025-01-15T00:00:00-05:00'
                        results:
                          type: array
                          items:
                            type: object
                            properties:
                              username:
                                type: string
                                description: >-
                                  Team username within the organization —
                                  identifies which team incurred this line item.
                                example: acme-ml-team
                              product:
                                type: string
                                enum:
                                  - model_apis
                                  - serverless
                                  - compute
                                description: >-
                                  Product line the usage belongs to:
                                  'model_apis' (fal Model APIs — endpoint
                                  calls), 'serverless' (fal Serverless SDK
                                  billing), or 'compute' (fal Compute — raw
                                  instances).
                                example: model_apis
                              endpoint_id:
                                type: string
                                description: >-
                                  Identifier of the billed resource. For
                                  'model_apis' this is the endpoint slug (e.g.,
                                  'fal-ai/flux/dev'); for 'serverless' it is the
                                  SDK billing line; for 'compute' it is the
                                  machine type line (e.g., 'type:
                                  gpu_1x_h100_sxm5 (my-app, prod)').
                                example: fal-ai/flux/dev
                              unit:
                                type: string
                                description: >-
                                  The billing unit (e.g., 'image', 'video', or a
                                  GPU/compute unit for some models)
                                example: image
                              quantity:
                                type: number
                                minimum: 0
                                description: >-
                                  Quantity of usage in the specified billing
                                  unit
                                example: 4
                              unit_price:
                                type: number
                                minimum: 0
                                description: >-
                                  Unit price used to compute charges for this
                                  line item
                                example: 0.1
                              percent_discount:
                                type:
                                  - number
                                  - 'null'
                                minimum: 0
                                maximum: 100
                                description: >-
                                  Percentage discount applied to this line item
                                  (e.g., 20 = 20% discount). Null when no
                                  percentage discount applies.
                                example: 20
                              cost_subtotal:
                                type: number
                                minimum: 0
                                description: Cost before discounts (quantity × unit_price)
                                example: 0.4
                              cost_discount:
                                type: number
                                minimum: 0
                                description: >-
                                  Discount applied to this line item
                                  (cost_subtotal − cost_total)
                                example: 0.08
                              cost_total:
                                type: number
                                minimum: 0
                                description: >-
                                  Amount charged after discounts (cost_subtotal
                                  − cost_discount)
                                example: 0.32
                              cost:
                                type: number
                                minimum: 0
                                description: >-
                                  Deprecated: use cost_total. Same value as
                                  cost_total.
                                deprecated: true
                                example: 0.32
                              currency:
                                type: string
                                minLength: 3
                                maxLength: 3
                                description: >-
                                  Three-letter currency code (ISO 4217, e.g.,
                                  'USD')
                                example: USD
                              auth_method:
                                type: string
                                description: >-
                                  Authentication method label resolved across
                                  the organization (e.g., 'my-key (owner:
                                  alice)', 'alice <alice@example.com>', or
                                  'None'). Only populated when 'auth_method' is
                                  included in the expand parameter. Falls back
                                  to the raw upstream label if the decoration
                                  service cannot resolve a row.
                                example: 'production-key (owner: acme-ml-team)'
                              auth_method_structured:
                                type: object
                                properties:
                                  detail:
                                    type: string
                                    description: >-
                                      Human-readable summary of the
                                      authentication method (key alias, login
                                      username, or 'User token'/'None').
                                  api_key_id:
                                    type: string
                                    description: >-
                                      API key ID, present when the request was
                                      made with an API key.
                                  login_username:
                                    type: string
                                    description: >-
                                      Team member login username (nickname),
                                      present when the request was made with a
                                      user login token resolvable to a team
                                      member.
                                required:
                                  - detail
                                description: >-
                                  Structured authentication method identity
                                  resolved across the organization. Only
                                  populated when 'auth_method_structured' is
                                  included in the expand parameter.
                            required:
                              - username
                              - product
                              - endpoint_id
                              - unit
                              - quantity
                              - unit_price
                              - percent_discount
                              - cost_subtotal
                              - cost_discount
                              - cost_total
                              - cost
                              - currency
                            description: Organization usage line item with team attribution
                          description: Usage records for this time bucket
                      required:
                        - bucket
                        - results
                      description: Time bucket with grouped usage records
                    description: >-
                      Time series usage data grouped by time bucket (when expand
                      includes 'time_series'). Each bucket contains all usage
                      records for that time period.
                  summary:
                    type: array
                    items:
                      type: object
                      properties:
                        username:
                          type: string
                          description: >-
                            Team username within the organization — identifies
                            which team incurred this line item.
                          example: acme-ml-team
                        product:
                          type: string
                          enum:
                            - model_apis
                            - serverless
                            - compute
                          description: >-
                            Product line the usage belongs to: 'model_apis' (fal
                            Model APIs — endpoint calls), 'serverless' (fal
                            Serverless SDK billing), or 'compute' (fal Compute —
                            raw instances).
                          example: model_apis
                        endpoint_id:
                          type: string
                          description: >-
                            Identifier of the billed resource. For 'model_apis'
                            this is the endpoint slug (e.g., 'fal-ai/flux/dev');
                            for 'serverless' it is the SDK billing line; for
                            'compute' it is the machine type line (e.g., 'type:
                            gpu_1x_h100_sxm5 (my-app, prod)').
                          example: fal-ai/flux/dev
                        unit:
                          type: string
                          description: >-
                            The billing unit (e.g., 'image', 'video', or a
                            GPU/compute unit for some models)
                          example: image
                        quantity:
                          type: number
                          minimum: 0
                          description: Quantity of usage in the specified billing unit
                          example: 4
                        unit_price:
                          type: number
                          minimum: 0
                          description: >-
                            Unit price used to compute charges for this line
                            item
                          example: 0.1
                        percent_discount:
                          type:
                            - number
                            - 'null'
                          minimum: 0
                          maximum: 100
                          description: >-
                            Percentage discount applied to this line item (e.g.,
                            20 = 20% discount). Null when no percentage discount
                            applies.
                          example: 20
                        cost_subtotal:
                          type: number
                          minimum: 0
                          description: Cost before discounts (quantity × unit_price)
                          example: 0.4
                        cost_discount:
                          type: number
                          minimum: 0
                          description: >-
                            Discount applied to this line item (cost_subtotal −
                            cost_total)
                          example: 0.08
                        cost_total:
                          type: number
                          minimum: 0
                          description: >-
                            Amount charged after discounts (cost_subtotal −
                            cost_discount)
                          example: 0.32
                        cost:
                          type: number
                          minimum: 0
                          description: >-
                            Deprecated: use cost_total. Same value as
                            cost_total.
                          deprecated: true
                          example: 0.32
                        currency:
                          type: string
                          minLength: 3
                          maxLength: 3
                          description: Three-letter currency code (ISO 4217, e.g., 'USD')
                          example: USD
                        auth_method:
                          type: string
                          description: >-
                            Authentication method label resolved across the
                            organization (e.g., 'my-key (owner: alice)', 'alice
                            <alice@example.com>', or 'None'). Only populated
                            when 'auth_method' is included in the expand
                            parameter. Falls back to the raw upstream label if
                            the decoration service cannot resolve a row.
                          example: 'production-key (owner: acme-ml-team)'
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
                        - product
                        - endpoint_id
                        - unit
                        - quantity
                        - unit_price
                        - percent_discount
                        - cost_subtotal
                        - cost_discount
                        - cost_total
                        - cost
                        - currency
                      description: >-
                        Aggregate organization usage statistics for the entire
                        date range
                    description: Aggregate statistics (when expand includes 'summary')
                required:
                  - next_cursor
                  - has_more
                description: >-
                  Response containing organization usage data with pagination
                  support
              example:
                time_series:
                  - bucket: '2025-01-15T00:00:00-05:00'
                    results:
                      - username: acme-ml-team
                        product: model_apis
                        endpoint_id: fal-ai/flux/dev
                        unit: image
                        quantity: 4
                        unit_price: 0.1
                        percent_discount: null
                        cost_subtotal: 0.4
                        cost_discount: 0
                        cost_total: 0.4
                        cost: 0.4
                        currency: USD
                      - username: acme-ml-team
                        product: compute
                        endpoint_id: 'type: gpu_1x_h100_sxm5 (my-app, production)'
                        unit: second
                        quantity: 3600
                        unit_price: 0.001
                        percent_discount: null
                        cost_subtotal: 3.6
                        cost_discount: 0
                        cost_total: 3.6
                        cost: 3.6
                        currency: USD
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
