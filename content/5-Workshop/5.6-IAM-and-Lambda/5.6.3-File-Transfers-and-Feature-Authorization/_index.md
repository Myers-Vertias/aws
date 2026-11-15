---
title: "Implement File Transfers and Feature Authorization"
date: 2026-10-16
weight: 3
chapter: false
pre: " <b> 5.6.3 </b> "
---

{{% notice info %}}
This section reuses existing browser and API evidence from sections 5.7.2, 5.8.2, 5.10.2, and 5.10.3 to avoid repeating screenshots.
{{% /notice %}}

## Objective

Implement presigned upload/download, Favorites, Tags, Trash, and sharing with server-side checks.

## Implementation steps

1. Generate a server-controlled S3 key and short-lived PUT URL only after validating identity, file type, and size. Verify the object before saving final metadata.
2. For download, check the caller is the owner or an active viewer before issuing a short-lived GET URL.
3. Store Favorites by user and document. Keep category/status codes stable and normalize tags consistently.
4. Trash is soft delete through `deletedAt`; restore clears it. Permanent delete must coordinate S3 object and related metadata cleanup.
5. Share creation must validate the recipient and reject self-sharing/duplicates. Revoke prevents new metadata/URL access. A previously issued presigned URL remains valid until expiry.

## Verified behavior and evidence

- **Presigned upload and download:** The successful S3 upload, saved document metadata, and download flow are recorded in section 5.8.2.
- **Resource authorization:** Section 5.7.2 records the unauthenticated `401` and the signed-in second account receiving `404` for an owner's unshared document.
- **Sharing:** The recipient's Shared With Me view and download interaction are recorded in section 5.10.2. A direct recipient download is also recorded in section 5.8.2.
- **Favorites, metadata, and Trash/restore:** These results are recorded in sections 5.8.2 and 5.10.3.

Share revocation, self-share/duplicate rejection, and attempts by a viewer to modify or delete a shared document were not verified. No behavior for those cases is claimed here.

## Expected result

Document features use backend data and permissions are enforced in Lambda.

## Troubleshooting

- A Viewer must not edit, share onward, or delete another user's document.
- Cross-service S3/DynamoDB operations are not automatically atomic; document recovery for partial failures.
