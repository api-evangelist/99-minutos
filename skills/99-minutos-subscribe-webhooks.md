---
name: Subscribe to shipment status webhooks
description: Register a webhook so 99minutos pushes shipment status changes in real time instead of polling the tracking endpoints.
api: openapi/99minutos-api-v3-openapi.json
operations: [oauth_token_create, post-api-v3-webhooks, get-api-v3-webhooks, get-api-v3-webhooks-evidences-by-tracking-id]
---

# Subscribe to shipment status webhooks (99minutos API v3)

Base URL: `https://delivery.99minutos.com` (or `https://sandbox.99minutos.com`). Webhooks are the recommended alternative to polling the tracking endpoints (which are capped at 8 req/s).

## 1. Authenticate
`POST /api/v3/oauth/token` (`oauth_token_create`) → bearer JWT.

## 2. Register the webhook
`POST /api/v3/webhooks` (`post-api-v3-webhooks`). You may configure **at most 2** webhook endpoints. Optionally scope delivery with `status_rules` (`status_version`, `allowed_status_names`) and set custom `headers`.

## 3. Verify configuration
`GET /api/v3/webhooks` (`get-api-v3-webhooks`) lists current configurations. Update with `PATCH /api/v3/webhooks/{webhook_id}` or remove with `DELETE /api/v3/webhooks/{webhook_id}`.

## 4. Handle notifications
Each notification is `application/json` with `StatusName`, `TrackingId`, `InternalKey`, and an ordered `Events[]` history (each event: `StatusCode`, `StatusName`, `Data.comment`, `Data.evidence[]`, `CreatedAt` in UTC). Delivery evidence photos arrive as URLs in `Data.evidence`.

## 5. Pull evidence on demand
`GET /api/v3/webhooks/evidences/{tracking_id}` (`get-api-v3-webhooks-evidences-by-tracking-id`) returns delivery/pickup evidence for a tracking id.

Status codes range 1001 (DRAFT) → 8004 (DAMAGED); terminal states include 4002 DELIVERED, 5002 RETURNED, 8003 CANCELLED.
