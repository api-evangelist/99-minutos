---
name: Create and track a shipment
description: Authenticate, resolve addresses, create and confirm an order, print the guide, and track the resulting shipment on the 99minutos last-mile API v3.
api: openapi/99minutos-api-v3-openapi.json
operations: [oauth_token_create, locations_create, orders_create, orders_confirm_create, documents_guides_create, shipments_tracking_retrieve]
---

# Create and track a shipment (99minutos API v3)

Base URL: `https://delivery.99minutos.com` (production) or `https://sandbox.99minutos.com` (sandbox). Each environment has independent credentials.

## 1. Authenticate
`POST /api/v3/oauth/token` (`oauth_token_create`) with `client_id` + `client_secret`. Use the returned `access_token` (a JWT, `expires_in` ~3599s) as `Authorization: Bearer <jwt>` on every subsequent call. Re-authenticate on `401`.

## 2. Resolve origin and destination
`POST /api/v3/locations` (`locations_create`) to exchange an address for a reusable `locationId`. Reuse that id as origin/destination so you don't resend full address details.

## 3. Create the order
`POST /api/v3/orders` (`orders_create`). If `draft` is `false` (default) the shipment is CONFIRMED immediately; if `draft` is `true` it starts in DRAFT and must be confirmed in step 4. Handle `400` (bad payload), `412` (precondition), `401` (auth).

## 4. Confirm (only if created as draft)
`POST /api/v3/orders/{orderid}/confirm` (`orders_confirm_create`) to flip `draft` to false and start the logistics flow.

## 5. Print the label
`POST /api/v3/documents/guides` (`documents_guides_create`) for a PDF guide (sizes: `letter`, `zebra`, `small`), or `POST /api/v3/documents/zpl` for ZPL.

## 6. Track
`GET /api/v3/shipments/tracking` (`shipments_tracking_retrieve`) by `trackingId` or `internalKey`. **Do not poll faster than 8 requests/second** (`429` otherwise) — prefer subscribing to webhooks (see the webhook skill) for status changes. Status flows through codes 1001→8004 (DRAFT, CONFIRMED, COLLECTED, STORED, ON_LINEHAUL, ON_ROAD_TO_DELIVERY, DELIVERED, …).

Every response carries a `traceId` (uuid) — capture it for support.
