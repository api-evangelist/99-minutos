---
name: Quote a shipping rate and check coverage
description: Authenticate, confirm serviceable coverage, and estimate shipping cost for an origin/destination on the 99minutos API v3 before creating an order.
api: openapi/99minutos-api-v3-openapi.json
operations: [oauth_token_create, calculate_shipping_rates, GetZipCodesByCountry]
---

# Quote a shipping rate and check coverage (99minutos API v3)

Base URL: `https://delivery.99minutos.com` (or `https://sandbox.99minutos.com`).

## 1. Authenticate
`POST /api/v3/oauth/token` (`oauth_token_create`) → bearer JWT (see the create-and-track skill for details).

## 2. (Optional) Verify coverage
`GET /api/v3/coverage/zipcodes/{country}` (`GetZipCodesByCountry`) returns the serviceable postal codes for a country, paginated. Use it to confirm the destination is in-network before quoting.

## 3. Estimate the rate
`POST /api/v3/shipping/rates` (`calculate_shipping_rates`) with origin, destination, package dimensions and weight. For countries other than Mexico leave `zipcode` empty and pass coordinates only. Handle `400` (invalid payload).

Convenience GET variants also exist for quick lookups: rates by address, by coordinates, by zipcodes, and package-size by dimensions (`/api/v3/shipping/rates/...`).

Every response carries a `traceId` (uuid).
