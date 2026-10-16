---
title: "Implement File Transfers and Feature Authorization"
date: 2026-10-16
weight: 3
chapter: false
pre: " <b> 5.6.3 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Implement presigned upload/download, Favorites, Tags, Trash, and sharing with server-side checks.

## Implementation steps

1. Generate a server-controlled S3 key and short-lived PUT URL only after validating identity, file type, and size. Verify the object before saving final metadata.
2. For download, check the caller is the owner or an active viewer before issuing a short-lived GET URL.
3. Store Favorites by user and document. Keep category/status codes stable and normalize tags consistently.
4. Trash is soft delete through `deletedAt`; restore clears it. Permanent delete must coordinate S3 object and related metadata cleanup.
5. Share creation must validate the recipient and reject self-sharing/duplicates. Revoke prevents new metadata/URL access. A previously issued presigned URL remains valid until expiry.

## Screenshot placeholder

**[Screenshot placeholder 5.6.3 — Tests for upload/download authorization, Favorites, Trash/restore, and share/revoke.]**

## Expected result

Document features use backend data and permissions are enforced in Lambda.

## Troubleshooting

- A Viewer must not edit, share onward, or delete another user's document.
- Cross-service S3/DynamoDB operations are not automatically atomic; document recovery for partial failures.
