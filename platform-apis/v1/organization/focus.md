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

# Organization FOCUS Report

> 
Returns a FOCUS compliant billing report as a CSV download, spanning every team
in your organization. Each invoiced billing customer is reported as a
`BillingAccount`. Under shared (pooled) billing — one billing customer covering
multiple teams — each row is additionally attributed to the calling team via the
`SubAccountId` / `SubAccountName` columns. `SubAccountId` is that team's own
billing customer ID, so both account columns share one identifier namespace.

> **Availability:** This endpoint is available to enterprise customers with FOCUS reports and organizations enabled. Contact your account team or support@fal.ai to request access.

Must be called with a key on the organization's root team.

Supports three data sources:
- **estimate**: Real-time usage estimates for a date range. Under pooled billing every row is attributed to the calling team.
- **tagged-estimate**: Those same estimates with the `Tags` column populated from the `X-Fal-Tags` tags set on your requests. Requires tagged reporting to be enabled for the organization; recent usage is delayed relative to `estimate`.
- **invoice**: Finalized invoice data for a billing month. Under pooled billing, per-team `SubAccount` attribution is available on endpoint (Model API) lines that carry a caller; app and compute lines carry no caller and have no SubAccount.

Use `team_username` to restrict the report to a single team. Under shared
(pooled) billing this is rejected for `source=invoice` — the invoice is issued
to one billing customer shared across teams and cannot be split per team; use
`source=estimate` for per-team figures.

**Invoice reports** default to the most recently available billing month.
**Usage estimates** default to the last 24 hours, with a maximum 90-day date range.
    

**Required permissions:** `account:users:read`, `billing:usage:read`, `auth:keys:read`. Key presets that include them: `BILLING`, `FULL`. See [key permissions](../../../documentation/model-apis/authentication/key-based.md#permissions).



## OpenAPI

````yaml /api-reference/platform-apis/openapi/v1.json get /organization/focus
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
  /organization/focus:
    get:
      tags:
        - Organization
      summary: Organization FOCUS Report
      description: >-

        Returns a FOCUS compliant billing report as a CSV download, spanning
        every team

        in your organization. Each invoiced billing customer is reported as a

        `BillingAccount`. Under shared (pooled) billing — one billing customer
        covering

        multiple teams — each row is additionally attributed to the calling team
        via the

        `SubAccountId` / `SubAccountName` columns. `SubAccountId` is that team's
        own

        billing customer ID, so both account columns share one identifier
        namespace.


        > **Availability:** This endpoint is available to enterprise customers
        with FOCUS reports and organizations enabled. Contact your account team
        or support@fal.ai to request access.


        Must be called with a key on the organization's root team.


        Supports three data sources:

        - **estimate**: Real-time usage estimates for a date range. Under pooled
        billing every row is attributed to the calling team.

        - **tagged-estimate**: Those same estimates with the `Tags` column
        populated from the `X-Fal-Tags` tags set on your requests. Requires
        tagged reporting to be enabled for the organization; recent usage is
        delayed relative to `estimate`.

        - **invoice**: Finalized invoice data for a billing month. Under pooled
        billing, per-team `SubAccount` attribution is available on endpoint
        (Model API) lines that carry a caller; app and compute lines carry no
        caller and have no SubAccount.


        Use `team_username` to restrict the report to a single team. Under
        shared

        (pooled) billing this is rejected for `source=invoice` — the invoice is
        issued

        to one billing customer shared across teams and cannot be split per
        team; use

        `source=estimate` for per-team figures.


        **Invoice reports** default to the most recently available billing
        month.

        **Usage estimates** default to the last 24 hours, with a maximum 90-day
        date range.
            

        **Required permissions:** `account:users:read`, `billing:usage:read`,
        `auth:keys:read`. Key presets that include them: `BILLING`, `FULL`. See
        [key
        permissions](https://fal.ai/docs/documentation/model-apis/authentication/key-based#permissions).
      operationId: getOrganizationFocusReport
      parameters:
        - schema:
            type: string
            enum:
              - invoice
              - estimate
              - tagged-estimate
            description: >-
              Report source. 'invoice' returns finalized invoice data for a
              billing month. 'estimate' returns real-time usage estimates for a
              date range. 'tagged-estimate' returns those same estimates with
              the Tags column populated from the X-Fal-Tags tags set on your
              requests; it requires tagged reporting to be enabled for the
              organization, and recent usage is delayed relative to 'estimate'.
            example: estimate
          required: true
          description: >-
            Report source. 'invoice' returns finalized invoice data for a
            billing month. 'estimate' returns real-time usage estimates for a
            date range. 'tagged-estimate' returns those same estimates with the
            Tags column populated from the X-Fal-Tags tags set on your requests;
            it requires tagged reporting to be enabled for the organization, and
            recent usage is delayed relative to 'estimate'.
          name: source
          in: query
        - schema:
            type: string
            pattern: ^\d{4}-\d{2}$
            description: >-
              Invoice billing month (YYYY-MM). The month the invoice was issued
              (e.g. '2025-02' for January charges). Used with source=invoice.
              Defaults to most recent available billing month.
            example: 2025-02
          required: false
          description: >-
            Invoice billing month (YYYY-MM). The month the invoice was issued
            (e.g. '2025-02' for January charges). Used with source=invoice.
            Defaults to most recent available billing month.
          name: billing_month
          in: query
        - schema:
            type: string
            pattern: ^\d{4}-\d{2}$
            description: >-
              Charge month (YYYY-MM) — selects line items by the period charges
              were incurred, capturing them even when split across invoices
              issued in different months. Alternative to billing_month. Used
              with source=invoice.
            example: 2025-01
          required: false
          description: >-
            Charge month (YYYY-MM) — selects line items by the period charges
            were incurred, capturing them even when split across invoices issued
            in different months. Alternative to billing_month. Used with
            source=invoice.
          name: charge_month
          in: query
        - schema:
            type: string
            description: >-
              Restrict the report to a single team (workspace nickname) in the
              organization. Omit to report across all teams.
            example: acme-ml-team
          required: false
          description: >-
            Restrict the report to a single team (workspace nickname) in the
            organization. Omit to report across all teams.
          name: team_username
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
      responses:
        '200':
          description: FOCUS compliant CSV report
          content:
            text/csv:
              schema:
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
