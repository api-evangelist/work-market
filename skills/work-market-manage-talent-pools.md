---
name: Manage WorkMarket talent pools
description: >-
  Organize contractors into WorkMarket talent pools (groups) — list pools,
  invite workers, and process applications.
api: openapi/work-market-employer-api-openapi.yml
operations: [listTalentPoolsAction, addOrInviteWorkersAction, applyToTalentPoolOnBehalfAction, approveWorkersAction, declineWorkersAction, removeWorkersAction]
generated: '2026-07-21'
method: generated
---

# Manage WorkMarket talent pools

## Steps
1. Enumerate pools with `listTalentPoolsAction` (`GET /v1/employer/groups/list`).
2. Invite or add workers with `addOrInviteWorkersAction`
   (`POST /v1/employer/groups/{groupId}/add_workers`).
3. Submit an application for a worker with `applyToTalentPoolOnBehalfAction`
   (`POST /v1/employer/groups/{groupId}/apply_on_behalf`).
4. Process pending applications with `approveWorkersAction`
   (`POST /v1/employer/groups/{groupId}/approve_workers`) or
   `declineWorkersAction` (`POST /v1/employer/groups/{groupId}/decline_workers`).
5. Remove members with `removeWorkersAction`
   (`POST /v1/employer/groups/{groupId}/remove_workers`).

## Rules
- Talent pools gate who can receive assignments; treat approve/decline/remove
  as consequential membership changes and keep an audit note of why.
- All responses use the `{meta, response}` envelope — check `meta.errors[]`
  and log `meta.requestId`. No idempotency key exists; make membership calls
  conditional on a fresh `listTalentPoolsAction` read.
