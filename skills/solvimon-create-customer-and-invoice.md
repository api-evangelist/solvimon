---
name: Create a customer and issue an invoice
description: Create (or upsert) a Solvimon customer and issue an invoice against them.
api: openapi/solvimon-configuration-api-openapi.yml, openapi/solvimon-transaction-api-openapi.yml
operations: [postCustomers, putCustomers, getCustomersByResourceIdOrReference, postInvoices]
---

# Create a customer and issue an invoice

Use the Solvimon billing platform to onboard a customer and bill them.

## Auth & environment
- Send your secret key in the `X-API-KEY` header on every call.
- Sandbox base URL: `https://test.api.solvimon.com/v1`. Production uses your dedicated host `https://[dedicated].solvimon.com/v1`.
- Set `Content-Type: application/json`.

## Steps
1. **Create the customer** — `POST /v1/customers` (`postCustomers`). Provide a stable `reference` you control; supply `type` (`ORGANIZATION` or `INDIVIDUAL`) and the organization/person block. To make the call safe to retry, add an `Idempotency-Key` header (V4 UUID). To create-or-update by your own reference instead, use `PUT /v1/customers` (`putCustomers`).
2. **Confirm** — `GET /v1/customers/{resourceIdOrReference}` (`getCustomersByResourceIdOrReference`) returns the customer with its `id` (prefix `cust_`) and `status`.
3. **Issue the invoice** — `POST /v1/invoices` (`postInvoices`) on the Transaction API, referencing the customer by id or reference and the billing entity. Add an `Idempotency-Key` header.

## Conventions to respect
- **Idempotency:** an `Idempotency-Key` is honored for 48h; replays return the stored response with `Idempotent-Replayed`. See `conventions/solvimon-conventions.yml`.
- **Errors:** failures return `{type, code, field, message}`; capture the `X-REQUEST-ID` response header for support. See `errors/solvimon-error-codes.yml`.
- **Dates:** ISO 8601, no milliseconds.
