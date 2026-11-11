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

# Usage

> 
Returns paginated usage records for your workspace with filters for endpoint,
user, date range, and auth method. Each item includes the billed unit
quantity, the pre-discount unit price and cost_subtotal, any percentage discount
applied, and the final cost_total (cost_subtotal − cost_discount).

**Key Features:**
- Usage data for all endpoints or filtered by specific endpoint(s)
- Flexible date range filtering
- User-specific usage tracking
- Detailed usage line items with unit quantity, price, and discount breakdown
- Paginated results for large datasets

**Tagged usage:** pass `source=tagged-estimate` to read the tagged aggregate
instead of the default estimate. Each row then carries a `tags` object built
from the `X-Fal-Tags` tags set on your requests, and `tag=key=value` filters
narrow the results — repeat the parameter to AND several pairs together.
Requires tagged reporting to be enabled for the account; recent usage is delayed
relative to the default source.

**Common Use Cases:**
- Generate usage reports for all endpoints or specific models
- Track usage patterns
- Monitor endpoint usage across different auth methods
- Build usage dashboards and visualizations

See [fal.ai docs](../../../documentation/model-apis/faq.md) for more details.
    

**Required permissions:** `billing:usage:read`, `auth:keys:read`, `account:users:read`. Key presets that include them: `BILLING`, `FULL`. See [key permissions](../../../documentation/model-apis/authentication/key-based.md#permissions).



## OpenAPI

````yaml /api-reference/platform-apis/openapi/v1.json get /models/usage
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
  /models/usage:
    get:
      tags:
        - Models
        - Usage
      summary: Usage
      description: >-

        Returns paginated usage records for your workspace with filters for
        endpoint,

        user, date range, and auth method. Each item includes the billed unit

        quantity, the pre-discount unit price and cost_subtotal, any percentage
        discount

        applied, and the final cost_total (cost_subtotal − cost_discount).


        **Key Features:**

        - Usage data for all endpoints or filtered by specific endpoint(s)

        - Flexible date range filtering

        - User-specific usage tracking

        - Detailed usage line items with unit quantity, price, and discount
        breakdown

        - Paginated results for large datasets


        **Tagged usage:** pass `source=tagged-estimate` to read the tagged
        aggregate

        instead of the default estimate. Each row then carries a `tags` object
        built

        from the `X-Fal-Tags` tags set on your requests, and `tag=key=value`
        filters

        narrow the results — repeat the parameter to AND several pairs together.

        Requires tagged reporting to be enabled for the account; recent usage is
        delayed

        relative to the default source.


        **Common Use Cases:**

        - Generate usage reports for all endpoints or specific models

        - Track usage patterns

        - Monitor endpoint usage across different auth methods

        - Build usage dashboards and visualizations


        See [fal.ai docs](https://fal.ai/docs/documentation/model-apis/faq) for
        more details.
            

        **Required permissions:** `billing:usage:read`, `auth:keys:read`,
        `account:users:read`. Key presets that include them: `BILLING`, `FULL`.
        See [key
        permissions](https://fal.ai/docs/documentation/model-apis/authentication/key-based#permissions).
      operationId: getUsage
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
            type: string
            enum:
              - estimate
              - tagged-estimate
            default: estimate
            description: >-
              Data source. 'estimate' is the default usage estimate.
              'tagged-estimate' reads the tagged aggregate instead, populating
              each row's 'tags' and enabling the 'tag' filter; it requires
              tagged reporting to be enabled for the account, and recent usage
              is delayed relative to 'estimate'.
            example: estimate
          required: false
          description: >-
            Data source. 'estimate' is the default usage estimate.
            'tagged-estimate' reads the tagged aggregate instead, populating
            each row's 'tags' and enabling the 'tag' filter; it requires tagged
            reporting to be enabled for the account, and recent usage is delayed
            relative to 'estimate'.
          name: source
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
              Filter by team member login username(s) (nickname). Accepts 1-50
              usernames. Supports comma-separated values:
              ?login_username=alice,bob or array syntax:
              ?login_username=alice&login_username=bob
            example:
              - alice
          required: false
          description: >-
            Filter by team member login username(s) (nickname). Accepts 1-50
            usernames. Supports comma-separated values:
            ?login_username=alice,bob or array syntax:
            ?login_username=alice&login_username=bob
          name: login_username
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
              Filter by X-Fal-Tags 'key=value' pairs. Accepts 1-10 filters:
              different keys are AND-combined (?tag=env=prod&tag=team=design),
              and repeating a key matches any of its values. Use the value
              '(untagged)' to match requests that did not set the key.
            example:
              - env=prod
          required: false
          description: >-
            Filter by X-Fal-Tags 'key=value' pairs. Accepts 1-10 filters:
            different keys are AND-combined (?tag=env=prod&tag=team=design), and
            repeating a key matches any of its values. Use the value
            '(untagged)' to match requests that did not set the key.
          name: tag
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
              'auth_method' to include a formatted authentication method label,
              and 'auth_method_structured' to include a machine-readable auth
              method object (detail, api_key_id, login_username). At least one
              of 'time_series' or 'summary' is required.
            example:
              - time_series
              - auth_method
          required: false
          description: >-
            Data to include in the response. Use 'time_series' for time-bucketed
            data, 'summary' for aggregate statistics, 'auth_method' to include a
            formatted authentication method label, and 'auth_method_structured'
            to include a machine-readable auth method object (detail,
            api_key_id, login_username). At least one of 'time_series' or
            'summary' is required.
          name: expand
          style: form
          explode: true
          in: query
      responses:
        '200':
          description: Usage data retrieved successfully
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
                        results:
                          type: array
                          items:
                            type: object
                            properties:
                              endpoint_id:
                                type: string
                                description: >-
                                  Endpoint identifier that was used (e.g.,
                                  'fal-ai/flux/dev')
                              unit:
                                type: string
                                description: >-
                                  The billing unit (e.g., 'image', 'video', or a
                                  GPU/compute unit for some models)
                              quantity:
                                type: number
                                minimum: 0
                                description: >-
                                  Quantity of usage in the specified billing
                                  unit
                              unit_price:
                                type: number
                                minimum: 0
                                description: Per-unit price before percentage discounts
                              percent_discount:
                                type:
                                  - number
                                  - 'null'
                                minimum: 0
                                maximum: 100
                                description: >-
                                  Percentage discount applied to this line item
                                  (e.g., 20 = 20% discount). Null when no
                                  percentage discount applies. Usage billed at
                                  different discount rates appears as separate
                                  rows, like unit_price.
                              cost_subtotal:
                                type: number
                                minimum: 0
                                description: Cost before discounts (quantity × unit_price)
                              cost_discount:
                                type: number
                                minimum: 0
                                description: >-
                                  Discount applied to this line item
                                  (cost_subtotal − cost_total)
                              cost_total:
                                type: number
                                minimum: 0
                                description: >-
                                  Amount charged after discounts (cost_subtotal
                                  − cost_discount)
                              cost:
                                type: number
                                minimum: 0
                                description: >-
                                  Deprecated: use cost_total. Same value as
                                  cost_total.
                                deprecated: true
                              currency:
                                type: string
                                minLength: 3
                                maxLength: 3
                                description: >-
                                  Three-letter currency code (ISO 4217, e.g.,
                                  'USD')
                              auth_method:
                                type: string
                                description: >-
                                  Authentication method label (e.g., 'Key 1',
                                  'Key 2', 'User token'). Only populated when
                                  'auth_method' is included in expand parameter.
                              tags:
                                type: object
                                additionalProperties:
                                  type: string
                                description: >-
                                  Activated X-Fal-Tags key/values represented by
                                  this row. Only populated when
                                  source=tagged-estimate; a key the row's
                                  requests did not set is omitted, so a row that
                                  set none reports {}.
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
                                  Structured authentication method identity.
                                  Only populated when 'auth_method_structured'
                                  is included in expand parameter.
                            required:
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
                            description: Usage line item with billing details
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
                        endpoint_id:
                          type: string
                          description: >-
                            Endpoint identifier that was used (e.g.,
                            'fal-ai/flux/dev')
                        unit:
                          type: string
                          description: >-
                            The billing unit (e.g., 'image', 'video', or a
                            GPU/compute unit for some models)
                        quantity:
                          type: number
                          minimum: 0
                          description: Quantity of usage in the specified billing unit
                        unit_price:
                          type: number
                          minimum: 0
                          description: Per-unit price before percentage discounts
                        percent_discount:
                          type:
                            - number
                            - 'null'
                          minimum: 0
                          maximum: 100
                          description: >-
                            Percentage discount applied to this line item (e.g.,
                            20 = 20% discount). Null when no percentage discount
                            applies. Usage billed at different discount rates
                            appears as separate rows, like unit_price.
                        cost_subtotal:
                          type: number
                          minimum: 0
                          description: Cost before discounts (quantity × unit_price)
                        cost_discount:
                          type: number
                          minimum: 0
                          description: >-
                            Discount applied to this line item (cost_subtotal −
                            cost_total)
                        cost_total:
                          type: number
                          minimum: 0
                          description: >-
                            Amount charged after discounts (cost_subtotal −
                            cost_discount)
                        cost:
                          type: number
                          minimum: 0
                          description: >-
                            Deprecated: use cost_total. Same value as
                            cost_total.
                          deprecated: true
                        currency:
                          type: string
                          minLength: 3
                          maxLength: 3
                          description: Three-letter currency code (ISO 4217, e.g., 'USD')
                        auth_method:
                          type: string
                          description: >-
                            Authentication method label (e.g., 'Key 1', 'Key 2',
                            'User token'). Only populated when 'auth_method' is
                            included in expand parameter.
                        tags:
                          type: object
                          additionalProperties:
                            type: string
                          description: >-
                            Activated X-Fal-Tags key/values represented by this
                            row. Only populated when source=tagged-estimate; a
                            key the row's requests did not set is omitted, so a
                            row that set none reports {}.
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
                            Structured authentication method identity. Only
                            populated when 'auth_method_structured' is included
                            in expand parameter.
                      required:
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
                      description: Aggregate usage statistics for the entire date range
                    description: Aggregate statistics (when expand includes 'summary')
                required:
                  - next_cursor
                  - has_more
                description: Response containing usage data with pagination support
              example:
                time_series:
                  - bucket: '2025-01-15T00:00:00-05:00'
                    results:
                      - endpoint_id: fal-ai/flux/dev
                        unit: image
                        quantity: 4
                        unit_price: 0.1
                        percent_discount: 20
                        cost_subtotal: 0.4
                        cost_discount: 0.08
                        cost_total: 0.32
                        cost: 0.32
                        currency: USD
                        auth_method: Production Key
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
