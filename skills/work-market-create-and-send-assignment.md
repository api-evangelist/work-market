---
name: Create and send a WorkMarket assignment
description: >-
  Authenticate against the WorkMarket Employer API, create an assignment, send
  it to workers, and track it through acceptance.
api: openapi/work-market-employer-api-openapi.yml
operations: [request, create, send, get, listAssignmentsAction, listUpdatedAction]
generated: '2026-07-21'
method: generated
---

# Create and send a WorkMarket assignment

## Auth
1. Exchange the account's API `token` and `secret` for an access token with
   `request` (`POST /v1/employer/authorization/request?token=...&secret=...`).
2. Send the returned access token on every call as `Authorization: Bearer <token>`.
   Sandbox accounts live on `https://api.dev.workmarket.com`; production is
   `https://www.workmarket.com`.

## Steps
1. Create the assignment with `create` (`POST /v1/employer/assignments/create`)
   — title, description, schedule, pricing, and location are the core fields;
   custom fields and labels can be set after creation.
2. Send it to workers or talent pools with `send`
   (`POST /v1/employer/assignments/{id}/send`).
3. Poll `get` (`GET /v1/employer/assignments/get`) for a single assignment, or
   `listUpdatedAction` (`GET /v1/employer/assignments/list_updated`) to pick up
   everything that changed since the last sync. Prefer configuring webhooks
   (Accepted / Sent / Completed events) over polling — see
   `../asyncapi/work-market-webhooks.yml`.
4. Enumerate assignments with `listAssignmentsAction`
   (`GET /v1/employer/assignments/list`) using `start`/`limit` offset paging.

## Rules
- Every response is wrapped as `{meta, response}`; treat `meta.status_code`
  and `meta.errors[]` as the error contract and log `meta.requestId`.
- There is NO idempotency key: never blindly retry a `create` — reconcile with
  `listUpdatedAction` first to avoid duplicate assignments.
