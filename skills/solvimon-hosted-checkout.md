---
name: Mint a hosted checkout / portal session
description: Create a short-lived portal object for Solvimon's hosted checkout or embedded SDK.
api: openapi/solvimon-transaction-api-openapi.yml
operations: [postPortalUrls, getPortalUrlsByResourceId, postPortalUrlsByResourceIdRevoke]
---

# Mint a hosted checkout / portal session

Create a `PORTAL_URL` on your backend so a customer can self-serve checkout, either via Solvimon's hosted page or the embedded Frontend SDK.

## Auth & environment
- `X-API-KEY` header on the **backend only** — the secret key must never reach the browser.
- Sandbox `https://test.api.solvimon.com/v1`.

## Steps
1. **Create the portal object** — `POST /v1/portal-urls` (`postPortalUrls`, Transaction API). Choose a `type` (e.g. `INIT_PRICING_PLAN_SUBSCRIPTION`) and pass the target `pricing_plan_id`. The response is a `PORTAL_URL` (id prefix `purl_`) carrying a short-lived `token`.
2. **Hand the token to the frontend** — only the portal object/`token` goes to the browser. Mount it with the Frontend SDK: `createSolvimonCore({ environment: 'TEST' }).createScreen('checkout', { portalObject })` (see `components/solvimon-components.yml`), or redirect to the hosted checkout.
3. **Lifecycle** — portal objects are short-lived; mint a fresh one per session, never cache or share across customers. Revoke early with `POST /v1/portal-urls/{resourceId}/revoke` (`postPortalUrlsByResourceIdRevoke`).

## Errors
- `{type, code, field, message}` envelope; capture `X-REQUEST-ID`.
