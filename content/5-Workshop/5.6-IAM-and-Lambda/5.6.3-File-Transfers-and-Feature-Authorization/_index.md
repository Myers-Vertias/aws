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

Follow the steps in order. Read the action, its explanation, and the expected check before moving to the next step.


### Step 1: In backend/src/index.mjs, build the S3 key from Cognito

In `backend/src/index.mjs`, build the S3 key from Cognito `sub` and a server-generated ID; validate file type/size before returning a PUT URL. On completion, use HeadObject before writing metadata.

### Step 2: For download, sharing, favorites, and Trash, identify the

For download, sharing, favorites, and Trash, identify the caller from claims and check permissions before issuing URLs or changing records. The frontend calls routes through `src/services/documentService.js`.

### Step 3: Favorites are a user/document relation

Favorites are a user/document relation; Trash is a soft delete using `deletedAt`, and restore removes that attribute. Hard delete must coordinate the S3 object, metadata, shares, and favorites.

### Step 4: Share creation resolves email to Cognito sub

Share creation resolves email to Cognito `sub`; only the owner can grant/revoke access. A Viewer can read/download an active shared file; an issued URL remains usable until it expires.

### Step 5: After Lambda changes, build/deploy with SAM

After Lambda changes, build/deploy with SAM. The frontend uses API URL and Cognito IDs from Outputs; it never accesses DynamoDB/S3 with browser credentials.

### Example: create the key and upload URL

The key is built from the user's `sub` and a server-generated document ID; the browser does not choose the key:

```javascript
const documentId = randomUUID();
const s3Key = `users/${caller.sub}/documents/${documentId}`;
const uploadUrl = await getSignedUrl(s3, new PutObjectCommand({
  Bucket: process.env.DOCUMENTS_BUCKET,
  Key: s3Key,
  ContentType: contentType,
  Metadata: { "document-id": documentId },
}), { expiresIn: 300 });
```

## Verified behavior and evidence

- **Presigned upload and download:** The successful S3 upload, saved document metadata, and download flow are recorded in section 5.8.2.
- **Resource authorization:** Section 5.7.2 records the unauthenticated `401` and the signed-in second account receiving `404` for an owner's unshared document.
- **Sharing:** The recipient's Shared With Me view and download interaction are recorded in section 5.10.2. A direct recipient download is also recorded in section 5.8.2.
- **Favorites, metadata, and Trash/restore:** These results are recorded in sections 5.8.2 and 5.10.3.

The local upload form rejected a file over its displayed 20 MB limit, and a separate authenticated request verified that the deployed `POST /documents/upload-url` rejects 20 MiB + 1 byte with HTTP `400` before issuing an S3 upload URL; see section 5.10.1.

## Expected result

Document features use backend data and permissions are enforced in Lambda.

## Troubleshooting

- A Viewer must not edit, share onward, or delete another user's document.
- Cross-service S3/DynamoDB operations are not automatically atomic; document recovery for partial failures.
