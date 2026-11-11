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
Time-bucketed, aggregated serverless compute usage for **your own account** —
the machine-seconds your deployed serverless apps consumed, priced with your
machine prices and net of discounts. This matches the serverless portion of the
dashboard usage view. Unlike `/v1/models/usage` (which reports model API
endpoint calls), this reports the `sdk_billing_event` compute spend of the apps
you run on fal Serverless. Results are always scoped to the apps you own.

**Filtering by app:**
- `app` — exact match on one or more app names (comma-separated or repeated,
  up to 50): `?app=my-app-dev,my-app-prod`. Use the value exactly as it appears
  in the response `app` field.
- `search` — case-insensitive substring match on the app name, for when you
  know the name but not the exact environment/version suffix: `?search=my-app`
  returns every `my-app*` variant.
- Provide both to AND them. Omit both to return every app you own — useful for
  discovering the exact app names to filter on.

**Expansions:**
- `time_series`: usage grouped into time buckets (default)
- `summary`: a single aggregate row per app × machine type across the window

**Notes:**
- Each row is machine-seconds (`unit` is always `"second"`); surge and
  non-surge usage of the same app/machine come back as separate rows
  (`is_surge`), so sum across them for a per-app total.
- Time-series `bucket` timestamps are returned in the `timezone` you request
  (ISO 8601 with offset, e.g. `2025-01-15T00:00:00-05:00`), which also controls
  how usage is grouped into buckets.

**Common Use Cases:**
- Track your serverless apps' compute consumption and cost over time
- Break down spend per app, environment, and machine type
- Export usage to your own billing/observability tooling
    

**Required permissions:** `billing:usage:read`. Key presets that include them: `BILLING`, `READONLY`, `FULL`. See [key permissions](../../../documentation/model-apis/authentication/key-based.md#permissions).



## OpenAPI

````yaml /api-reference/platform-apis/openapi/v1.json get /serverless/usage
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
  /serverless/usage:
    get:
      tags:
        - Serverless
        - Usage
      summary: Usage
      description: >-

        Time-bucketed, aggregated serverless compute usage for **your own
        account** —

        the machine-seconds your deployed serverless apps consumed, priced with
        your

        machine prices and net of discounts. This matches the serverless portion
        of the

        dashboard usage view. Unlike `/v1/models/usage` (which reports model API

        endpoint calls), this reports the `sdk_billing_event` compute spend of
        the apps

        you run on fal Serverless. Results are always scoped to the apps you
        own.


        **Filtering by app:**

        - `app` — exact match on one or more app names (comma-separated or
        repeated,
          up to 50): `?app=my-app-dev,my-app-prod`. Use the value exactly as it appears
          in the response `app` field.
        - `search` — case-insensitive substring match on the app name, for when
        you
          know the name but not the exact environment/version suffix: `?search=my-app`
          returns every `my-app*` variant.
        - Provide both to AND them. Omit both to return every app you own —
        useful for
          discovering the exact app names to filter on.

        **Expansions:**

        - `time_series`: usage grouped into time buckets (default)

        - `summary`: a single aggregate row per app × machine type across the
        window


        **Notes:**

        - Each row is machine-seconds (`unit` is always `"second"`); surge and
          non-surge usage of the same app/machine come back as separate rows
          (`is_surge`), so sum across them for a per-app total.
        - Time-series `bucket` timestamps are returned in the `timezone` you
        request
          (ISO 8601 with offset, e.g. `2025-01-15T00:00:00-05:00`), which also controls
          how usage is grouped into buckets.

        **Common Use Cases:**

        - Track your serverless apps' compute consumption and cost over time

        - Break down spend per app, environment, and machine type

        - Export usage to your own billing/observability tooling
            

        **Required permissions:** `billing:usage:read`. Key presets that include
        them: `BILLING`, `READONLY`, `FULL`. See [key
        permissions](https://fal.ai/docs/documentation/model-apis/authentication/key-based#permissions).
      operationId: serverlessGetUsage
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
              Filter to one or more serverless apps, matched exactly against the
              `app` value in the response (deployed name, owner prefix
              stripped). Accepts a comma-separated list or repeated parameter
              (1-50). For partial/name-only matching use `search`.
            example:
              - autohdr-raw-to-jpg-dev
          required: false
          description: >-
            Filter to one or more serverless apps, matched exactly against the
            `app` value in the response (deployed name, owner prefix stripped).
            Accepts a comma-separated list or repeated parameter (1-50). For
            partial/name-only matching use `search`.
          name: app
          style: form
          explode: true
          in: query
        - schema:
            type: string
            description: >-
              Case-insensitive substring match on the app name — returns every
              app whose name contains this term (e.g. `search=autohdr` matches
              all `autohdr-*` apps across environments). Combined with `app` via
              AND when both are given.
            example: autohdr-raw-to-jpg
          required: false
          description: >-
            Case-insensitive substring match on the app name — returns every app
            whose name contains this term (e.g. `search=autohdr` matches all
            `autohdr-*` apps across environments). Combined with `app` via AND
            when both are given.
          name: search
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
              time-bucketed data and 'summary' for aggregate statistics across
              the entire window. At least one is required.
            example:
              - time_series
              - summary
          required: false
          description: >-
            Data to include in the response. Use 'time_series' for time-bucketed
            data and 'summary' for aggregate statistics across the entire
            window. At least one is required.
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
                              app:
                                type:
                                  - string
                                  - 'null'
                                description: >-
                                  Deployed serverless app name (the billing
                                  owner prefix is stripped, e.g.
                                  'autohdr-raw-to-jpg-dev'). May include an
                                  environment/version suffix. Null for line
                                  items without an app attribution.
                              environment:
                                type:
                                  - string
                                  - 'null'
                                description: >-
                                  Deployment environment of the app (e.g.,
                                  'production') when present on the billing line
                                  item, null otherwise.
                              machine_type:
                                type: string
                                description: >-
                                  Machine type the usage ran on (e.g.,
                                  'GPU-H100', 'L')
                              unit:
                                type: string
                                description: >-
                                  The billing unit — always 'second'
                                  (machine-seconds)
                              quantity:
                                type: number
                                minimum: 0
                                description: >-
                                  Quantity of usage in the specified billing
                                  unit
                              unit_price:
                                type: number
                                minimum: 0
                                description: >-
                                  Per-second price for this machine type before
                                  percentage discounts, including any
                                  customer-specific machine pricing
                              net_unit_price:
                                type: number
                                minimum: 0
                                description: >-
                                  Deprecated: use cost_total ÷ quantity.
                                  Per-second price after the percentage
                                  discount; equal to unit_price when no
                                  percentage discount applies.
                                deprecated: true
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
                              is_surge:
                                type: boolean
                                description: >-
                                  Whether this usage was billed at surge
                                  pricing. Surge and non-surge usage of the same
                                  machine type appear as separate rows.
                            required:
                              - app
                              - environment
                              - machine_type
                              - unit
                              - quantity
                              - unit_price
                              - net_unit_price
                              - percent_discount
                              - cost_subtotal
                              - cost_discount
                              - cost_total
                              - cost
                              - currency
                              - is_surge
                            description: >-
                              Serverless compute usage line item with billing
                              details
                          description: Usage records for this time bucket
                      required:
                        - bucket
                        - results
                      description: Time bucket with grouped serverless usage records
                    description: >-
                      Time series usage data grouped by time bucket (when expand
                      includes 'time_series').
                  summary:
                    type: array
                    items:
                      type: object
                      properties:
                        app:
                          type:
                            - string
                            - 'null'
                          description: >-
                            Deployed serverless app name (the billing owner
                            prefix is stripped, e.g. 'autohdr-raw-to-jpg-dev').
                            May include an environment/version suffix. Null for
                            line items without an app attribution.
                        environment:
                          type:
                            - string
                            - 'null'
                          description: >-
                            Deployment environment of the app (e.g.,
                            'production') when present on the billing line item,
                            null otherwise.
                        machine_type:
                          type: string
                          description: >-
                            Machine type the usage ran on (e.g., 'GPU-H100',
                            'L')
                        unit:
                          type: string
                          description: The billing unit — always 'second' (machine-seconds)
                        quantity:
                          type: number
                          minimum: 0
                          description: Quantity of usage in the specified billing unit
                        unit_price:
                          type: number
                          minimum: 0
                          description: >-
                            Per-second price for this machine type before
                            percentage discounts, including any
                            customer-specific machine pricing
                        net_unit_price:
                          type: number
                          minimum: 0
                          description: >-
                            Deprecated: use cost_total ÷ quantity. Per-second
                            price after the percentage discount; equal to
                            unit_price when no percentage discount applies.
                          deprecated: true
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
                        is_surge:
                          type: boolean
                          description: >-
                            Whether this usage was billed at surge pricing.
                            Surge and non-surge usage of the same machine type
                            appear as separate rows.
                      required:
                        - app
                        - environment
                        - machine_type
                        - unit
                        - quantity
                        - unit_price
                        - net_unit_price
                        - percent_discount
                        - cost_subtotal
                        - cost_discount
                        - cost_total
                        - cost
                        - currency
                        - is_surge
                      description: Aggregate usage statistics for the entire date range
                    description: Aggregate statistics (when expand includes 'summary')
                required:
                  - next_cursor
                  - has_more
                description: >-
                  Response containing serverless usage data with pagination
                  support
              example:
                time_series:
                  - bucket: '2025-01-15T00:00:00-05:00'
                    results:
                      - app: my-app-prod
                        environment: null
                        machine_type: GPU-H100
                        unit: second
                        quantity: 1200
                        unit_price: 0.00099
                        net_unit_price: 0.00099
                        percent_discount: null
                        cost_subtotal: 1.188
                        cost_discount: 0
                        cost_total: 1.188
                        cost: 1.188
                        currency: USD
                        is_surge: false
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
