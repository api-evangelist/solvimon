---
name: Create a pricing plan and subscribe a customer
description: Build a pricing plan and start a subscription for a Solvimon customer.
api: openapi/solvimon-configuration-api-openapi.yml
operations: [postPricingPlans, putPricingPlans, postPricingPlanSubscriptions]
---

# Create a pricing plan and subscribe a customer

Stand up a hybrid pricing plan (seats + usage + credits) and subscribe a customer.

## Auth & environment
- `X-API-KEY` header; sandbox `https://test.api.solvimon.com/v1`.

## Steps
1. **Create the pricing plan** — `POST /v1/pricing-plans` (`postPricingPlans`). Define recurring/usage-based/tiered pricings and any included volumes. Use `PUT /v1/pricing-plans` (`putPricingPlans`) to upsert by your reference. Add an `Idempotency-Key`.
2. **Subscribe the customer** — `POST /v1/pricing-plan-subscriptions` (`postPricingPlanSubscriptions`), referencing the customer and the pricing plan version, with `start_at` (ISO 8601). Add an `Idempotency-Key`.
3. **Read back** — list with `GET /v1/pricing-plan-subscriptions`, orderable by `start_at`, `status`, `created_at` and filterable by date range and `status`.

## Conventions
- **Idempotency-Key** honored 48h (`conventions/solvimon-conventions.yml`).
- **Pagination** is `page`/`limit` with a `links` block.
- **Errors:** `{type, code, field, message}`; keep `X-REQUEST-ID`.
