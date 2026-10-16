---
title: "Design the Data Model and API Contract"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.2.2 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Define data keys, endpoints, and Owner/Viewer rules so frontend and backend share one contract.

## Implementation steps

1. Use Cognito `sub` as the stable internal user ID. Treat email as an account attribute, not the main authorization identity.
2. Documents: `documentId` plus `ownerSub`, `s3Key`, filename, type, size, category, tags, description, timestamps, and optional `deletedAt`. Query an owner index for My Documents.
3. Shares: partition key `documentId`, sort key `recipientSub`, plus a recipient index for Shared With Me. Favorites: `userSub` + `documentId` so favorites are per user.
4. Define routes for documents CRUD, upload/download URLs, favorites, Trash/restore/permanent delete, shared listing, share creation/revocation.
5. Owner may manage a document; Viewer may view/download an active shared document only. Lambda must enforce permissions on every protected operation.

## Screenshot placeholder

**[Screenshot placeholder 5.2.2 — Reviewed data model, endpoint list, and Owner/Viewer permission matrix.]**

## Expected result

Table keys, access patterns, API routes, and permission rules are documented before resource creation.

## Troubleshooting

- Use language-independent codes such as `finance` and `active`; translate only their UI labels.
- Do not rely on frontend route guards or hidden buttons as security controls.
