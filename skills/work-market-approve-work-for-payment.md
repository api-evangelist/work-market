---
name: Approve completed WorkMarket work for payment
description: >-
  Review assignments a worker has submitted, approve or reject them for
  payment, and rate the worker.
api: openapi/work-market-employer-api-openapi.yml
operations: [listAssignmentsAction, approvePayment, rejectPayment, saveSinglePendingResourceRatingData]
generated: '2026-07-21'
method: generated
---

# Approve completed WorkMarket work for payment

## Steps
1. Find work pending approval with `listAssignmentsAction`
   (`GET /v1/employer/assignments/list`) — completed work sits in the
   "Pending Approval" status (the webhook event for this transition is
   `Completed`). Page with `start`/`limit`.
2. Approve with `approvePayment`
   (`POST /v1/employer/assignments/approve_payment`) — this generates the
   invoice and triggers the `Approved` (and later `Paid`) webhook events.
3. If the submission is not acceptable, send it back with `rejectPayment`
   (`POST /v1/employer/assignments/reject_payment`) and add context with a
   note so the worker knows what to fix.
4. Rate the worker with `saveSinglePendingResourceRatingData`
   (`POST /v1/employer/assignments/rate_assignment`).

## Rules
- Payment approval moves real money to the worker; surface the assignment id,
  worker, and amount to a human before calling `approvePayment` when acting
  autonomously.
- Check `meta.status_code` / `meta.errors[]` in the response envelope; there
  is no idempotency key, so verify state via `listAssignmentsAction` before
  retrying any POST.
