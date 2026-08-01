---
name: Ingest usage events against a meter
description: Define a meter and stream usage events into Solvimon for usage-based billing.
api: openapi/solvimon-event-api-openapi.yml, openapi/solvimon-configuration-api-openapi.yml
operations: [postMeters, postEventsIngest, postIngestMeterData]
---

# Ingest usage events against a meter

Meter your product's usage so Solvimon can price and invoice it.

## Auth & environment
- `X-API-KEY` header; sandbox `https://test.api.solvimon.com/v1`.

## Steps
1. **Create a meter** — `POST /v1/meters` (`postMeters`, Configuration API). Give it a `reference` (e.g. `card_issuing`) and define its meter values/properties.
2. **Ingest events** — `POST /v1/events/ingest` (`postEventsIngest`, Event API). Each event carries a unique `reference`, the `meter_reference`, `customer_reference`, and `meter_values`/`meter_properties`. Bulk meter data can be sent via `POST /v1/ingest/meter-data` (`postIngestMeterData`).
3. **Attribute to a subscription (optional)** — pass a pricing plan subscription id/reference on ingest so usage is booked against one subscription.

## Idempotency (events are special)
- Events use **reference-based idempotency**, not the `Idempotency-Key` header. Sending the same `reference` again returns `201` each time but only one event takes effect; configure a webhook to be notified of duplicates. See `conventions/solvimon-conventions.yml`.

## Errors
- `{type, code, field, message}` envelope; unmatched events are surfaced separately in the platform. Capture `X-REQUEST-ID`.
