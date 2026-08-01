---
name: Provision an accessWidget license for a domain
description: Look up whether a partner account already holds an accessWidget license for a domain, then create it or update the existing one, handling the 409 domain-conflict path correctly.
api: openapi/accessibe-partners-openapi-original.yml
generated: '2026-07-31'
method: generated
source: openapi/accessibe-partners-openapi-original.yml
operations:
  - GET /accounts/{accountId}/access-widget-licenses
  - POST /accounts/{accountId}/access-widget-licenses
  - PATCH /accounts/{accountId}/access-widget-licenses/{licenseId}
note: The harvested specification declares no operationIds. Steps are grounded in the real method + path pairs; the parenthesised ids are the ones assigned by overlays/accessibe-partners-overlay.yaml and are not present in the provider's own document.
---

# Provision an accessWidget license for a domain

Base URL: `https://dashboard.accessibe.com/api/v1/partners`

## Before you start

- Send the account API key in the **`x-api-key`** header on every request. The key is
  found in the Customer Portal under *Account Settings → Subscriptions API/CSV access*.
- The key must match the `accountId` in the request path, and that account must be
  enabled as a **partner account**. A mismatch returns `403` with
  `Could not find a partner account with the provided API key` or
  `Account does not have access to partners API`.
- `planSKU` is required on create and must be one of: `1` Standard, `2` Advanced Plus,
  `3` Enterprise, `5` Advanced, `21` Micro, `22` Growth, `23` Scale. Any other value
  returns `400` with `planSKU can only be 1, 2, 3, 5, 21, 22, 23`.

## Steps

1. **Check for an existing license on the domain.**
   `GET /accounts/{accountId}/access-widget-licenses?domain={domain}`
   (`listAccessWidgetLicenses`). A `404` with `Licenses not found` means the account
   has no license for that domain — this is an expected outcome, not a failure.
   Add `expand[]=settings` if you also need `settings`, `maxImagesReached` and
   `maxImagesLimit`.

2. **If no license exists, create one.**
   `POST /accounts/{accountId}/access-widget-licenses` (`createAccessWidgetLicense`)
   with a `CreateLicense` body. Required: `domain`, `planSKU`. Optional: `contactName`,
   `contactEmail`, `contactPhone`, `contactCountryCode`, `discountEnabled`, `monthly`,
   `activate`, `longTrial`, `settings`. Success is `201`.

3. **If a license already exists, update it instead of creating a second one.**
   `PATCH /accounts/{accountId}/access-widget-licenses/{licenseId}`
   (`updateAccessWidgetLicense`) with an `UpdateLicense` body. Any field you omit is
   left unchanged. Use `newDomain` to move a license to a different domain, and
   `disable` to deactivate it. Success is `200`.

4. **Handle `409` as the create-versus-update signal.** `Domain already being used`
   or `Domain already exists in account` means step 1 raced or was skipped: re-run
   step 1 to resolve the `licenseId`, then go to step 3. Never retry the `POST`.

5. **Read back what you wrote.**
   `GET /accounts/{accountId}/access-widget-licenses/{licenseId}`
   (`getAccessWidgetLicense`) and confirm `status` is one of `active`, `trial`,
   `grace`, `expired` as expected.

## Rules

- **Errors are not RFC 9457.** Every failure is `application/json` shaped
  `{"success": false, "message": "..."}`. Branch on the HTTP status first; the
  `message` string is human-readable, not a stable machine code. See
  `errors/accessibe-problem-types.yml`.
- **Do not retry writes blindly.** Single-license `POST` and `PATCH` declare **no**
  `Idempotency-Key` parameter — only the batch endpoints do. A retried `POST` after a
  timeout can produce a duplicate or a `409`. Re-read with step 1 before retrying.
- **Paginate list responses.** `limit` defaults to `10`. Follow the returned
  `nextToken` until it is absent. See `conventions/accessibe-conventions.yml`.
- No rate-limit headers and no `429` response are documented; back off conservatively.
