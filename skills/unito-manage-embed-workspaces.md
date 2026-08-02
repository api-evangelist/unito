---
name: Manage Unito embed workspaces
description: Create, replace, and inspect the Unito workspaces backing your embed,
  controlling access status and billing metadata.
api: openapi/unito-embeds-openapi-original.json
operations:
- GET /embeds/{embedId}/workspaces
- GET /embeds/{embedId}/workspaces/{externalWorkspaceId}
- PUT /embeds/{embedId}/workspaces/{externalWorkspaceId}
generated: '2026-07-21'
method: generated
---

# Manage Unito embed workspaces

A workspace houses the flows of one account/organization/group in your
embedding application. Its key, `externalWorkspaceId`, must match the
`external_workspace_id` you pass in the Sync Embed iframe URL
(`https://app.unito.io/api/embed/<embed-id>`).

## Auth

Pass the embed API key issued by Unito in the `X-API-Key` header. Base URL
`https://api.unito.io`.

## Steps

1. Provision (or update) a workspace with
   `PUT /embeds/{embedId}/workspaces/{externalWorkspaceId}`. The body accepts
   `externalStatus` (`active` | `trialing` | `disabled`) and `externalData`
   (free-form name-value pairs Unito needs per your agreement, typically
   billing metadata such as an SKU).
2. PUT is **create-or-replace**: if the workspace exists it is fully replaced,
   so always send the complete set of properties and metadata on every call.
   You may create workspaces before any user first opens the embed — they will
   not appear in usage reports until first accessed.
3. Read back one workspace with
   `GET /embeds/{embedId}/workspaces/{externalWorkspaceId}`, or list them all
   with `GET /embeds/{embedId}/workspaces` (returns `total` plus `data[]`).
4. To cut off access when a customer churns, PUT the workspace with
   `externalStatus: disabled` — disabled workspaces stop syncing and are
   excluded from usage reports.

## Rules

- `422 Unprocessable Entity` signals a validation failure (e.g. unknown
  properties — the workspace body rejects additionalProperties).
- Errors use the `{message, details}` envelope
  (`errors/unito-problem-types.yml`); respect `429` back-off.
