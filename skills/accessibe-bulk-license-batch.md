---
name: Bulk-create or bulk-update accessWidget licenses safely
description: Submit up to 150 accessWidget licenses in one asynchronous batch with an idempotency key, poll the batch to completion, and reconcile the per-row success and failure lists.
api: openapi/accessibe-partners-openapi-original.yml
generated: '2026-07-31'
method: generated
source: openapi/accessibe-partners-openapi-original.yml
operations:
  - POST /accounts/{accountId}/access-widget-licenses/batch-create
  - POST /accounts/{accountId}/access-widget-licenses/batch-update
  - GET /accounts/{accountId}/access-widget-licenses/batch/{batchId}
note: The harvested specification declares no operationIds. Steps are grounded in the real method + path pairs; the parenthesised ids are the ones assigned by overlays/accessibe-partners-overlay.yaml and are not present in the provider's own document.
---

# Bulk-create or bulk-update accessWidget licenses safely

Base URL: `https://dashboard.accessibe.com/api/v1/partners`

## Before you start

- Authenticate with the **`x-api-key`** header; the key must belong to the `accountId`
  in the path and the account must be a partner account.
- **Maximum 150 licenses per request** (`licenses[]`, `minItems` 1, `maxItems` 150).
  Chunk larger sets. The CSV upload path in the Customer Portal has a different limit
  (250 rows) — do not carry it over to the API.
- These are the **only** two operations in this API that accept an `Idempotency-Key`.
  Use them for anything bulk.

## Steps

1. **Chunk the work** into batches of at most 150 `CreateLicense` (create) or
   `UpdateLicense` (update) objects.

2. **Generate one `Idempotency-Key` per chunk** and persist it alongside the chunk
   before sending. Reuse the same key on every retry of that chunk so the request is
   processed only once.

3. **Submit the batch.**
   - Create: `POST /accounts/{accountId}/access-widget-licenses/batch-create`
     (`batchCreateAccessWidgetLicenses`), body `{"licenses": [ ...CreateLicense ]}`.
   - Update: `POST /accounts/{accountId}/access-widget-licenses/batch-update`
     (`batchUpdateAccessWidgetLicenses`), body `{"licenses": [ ...UpdateLicense ]}`.

   Both return **`202 Accepted`** with a `batchId`. A `202` means *queued*, not *done*.

4. **Poll for completion.**
   `GET /accounts/{accountId}/access-widget-licenses/batch/{batchId}?batchId={batchId}`
   (`getAccessWidgetLicenseBatchStatus`). The endpoint answers **`202` while
   `status` is `processing`** and **`200` when `status` is `completed`**. Poll with
   backoff until you see `200` / `completed`.

5. **Reconcile per row.** The `GetBatchStatus` payload carries `operation`
   (`create` | `update`), `hasErrors`, `total`, `totalSuccesses`, `totalFailures`,
   and two arrays:
   - `successLicenses[]` → `{domain, licenseId, getLicenseURL}` — persist `licenseId`
     against your own record for the domain.
   - `failedLicenses[]` → `{domain, message}` — the per-row reason. Domain conflicts
     appear **here**, not as an HTTP `409`, because the batch endpoints declare no
     `409` response.

6. **Retry only the failures.** Build a new chunk from `failedLicenses[]`, with a
   **new** `Idempotency-Key`, after fixing the cause in `message`.

## Rules

- Never treat `202` as success. Always poll to `completed`.
- Never reuse an `Idempotency-Key` across different payloads, and never generate a new
  one when retrying the *same* payload.
- A batch can partially succeed: `hasErrors: true` with a non-zero `totalSuccesses` is
  normal. Do not roll the whole chunk back on a partial failure.
- Errors at the request level use the `{"success": false, "message": "..."}` envelope —
  see `errors/accessibe-problem-types.yml`.
