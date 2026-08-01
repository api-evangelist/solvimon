---
name: Configure and secure a webhook
description: Register a Solvimon webhook, roll its signing secret, and verify HMAC signatures.
api: openapi/solvimon-configuration-api-openapi.yml
operations: [postWebhooks, patchWebhooksByResourceIdSecretKey, postWebhooksByResourceIdSendTest]
---

# Configure and secure a webhook

Subscribe to Solvimon resource events with signed, at-least-once delivery.

## Auth & environment
- `X-API-KEY` header; sandbox `https://test.api.solvimon.com/v1`.

## Steps
1. **Register the webhook** — `POST /v1/webhooks` (`postWebhooks`). Provide your HTTPS (TLS 1.2/1.3) endpoint URL, `included_actions` (e.g. `INVOICE.CREATED`, `CUSTOMER.UPDATED`), optional `excluded_actions`, and `active: true`. Optionally set endpoint auth (API key header or basic auth). See the full resource/action list in `webhooks/solvimon-webhooks.yml`.
2. **Get a signing secret** — `PATCH /v1/webhooks/{resourceId}/secret-key` (`patchWebhooksByResourceIdSecretKey`) to obtain/roll the secret. A roll keeps both old and new secrets valid for 24h.
3. **Send a test delivery** — `POST /v1/webhooks/{resourceId}/send-test` (`postWebhooksByResourceIdSendTest`).

## Verifying deliveries
- Each payload carries `X-PAYLOAD-SIGNATURE-TIMESTAMP` and `X-PAYLOAD-SIGNATURE` (`v1=<hex>`). Compute HMAC-SHA256 over `"{timestamp}.{rawBody}"` and compare; reject timestamps older than ~5 minutes.
- **Ack fast:** return `2XX` within 10s or Solvimon retries on a backoff schedule (2m,4m,…,8h) for up to 7 days. Accept before processing.
- Deliveries are inspectable/resendable via the webhook-events API (`GET /v1/webhook-events`, `POST /v1/webhook-events/{id}/resend`).
