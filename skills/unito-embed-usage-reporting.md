---
name: Pull Unito embed usage reports
description: Retrieve flows-usage and workspaces-usage reports for a Unito embed
  and page through the results safely.
api: openapi/unito-embeds-openapi-original.json
operations:
- GET /embeds/{embedId}/reports/{reportType}
generated: '2026-07-21'
method: generated
---

# Pull Unito embed usage reports

Use the Unito Embed API to understand how users of your embedding application
benefit from Unito: how many flows they created, which tools they connect, and
how much they sync.

## Auth

Every request needs the embed API key issued by Unito in the `X-API-Key`
header (see `authentication/unito-authentication.yml`). The base URL is
`https://api.unito.io` (staging: `https://staging-api.unito.io`).

## Steps

1. Choose a report type. `reportType` is an enum: `flows-usage` (one record
   per flow, with `isActive`, `mappedFieldsCount`, `changesSyncedLast30d`, and
   exactly two `tools[]` entries) or `workspaces-usage` (one record per
   workspace, with `itemsKeptInSyncCount`, `flowsCount`, `toolPairsStats`).
2. Call `GET /embeds/{embedId}/reports/{reportType}`.
3. Paginate with the `pagination` query object: `offset` (default 0) and
   `limit` (default 1000, max 10000). The response merges `PaginatedResult`
   fields `total`, `pageSize`, and `hasNextPage` with the `data[]` records —
   keep requesting while `hasNextPage` is true, advancing `offset`.

## Rules

- Report data is refreshed every few hours, at most every 24h — do not poll
  aggressively; a `429 Too Many Requests` response means back off and retry.
- Workspaces with `externalStatus: disabled` are excluded from reports, and a
  workspace never accessed by a user is not returned at all.
- Errors arrive as `{message, details}` JSON (`errors/unito-problem-types.yml`);
  `401` means the `X-API-Key` is missing or invalid.
