---
title: "Implement Document Metadata Handlers"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.6.2 </b> "
---

{{% notice info %}}
The deployed handler behavior is documented from the Lambda implementation and existing API/browser evidence. No separate Lambda test-event screenshot was captured.
{{% /notice %}}

## Objective

Implement create/list/detail/update operations using the authenticated caller and the approved data model.

## Implementation steps

Follow the steps in order. Read the action, its explanation, and the expected check before moving to the next step.


### Step 1: Open backend/src/index.mjs and use the API Gateway

Open `backend/src/index.mjs` and use the API Gateway dispatcher to route by method/path; read the caller `sub` from authorizer-verified JWT claims. Never accept an owner ID from the frontend body.

### Step 2: Query the owner's list with OwnerIndex and fetch details

Query the owner's list with `OwnerIndex` and fetch details by `documentId`; check ownership or an active share record before returning data.

### Step 3: Verify the uploaded S3 object before saving metadata

The upload-completion API verifies the S3 object with `HeadObject`, then writes its metadata item to the Documents table. Keep metadata and bytes separate: DynamoDB stores file details, while S3 stores file contents.

### Step 4: Limit PATCH updates to approved metadata fields

PATCH metadata accepts only `description`, `category`, and `tags`; validate types/limits and allow only the owner to update an active file. Return consistent DTO/error JSON with HTTP status.

### Step 5: After code changes, run sam validate --lint and sam build

After code changes, run `sam validate --lint` and `sam build` from `backend/`, then deploy using section 5.1.3 after reviewing the change set.

### Example: read identity from JWT claims

This excerpt is from `backend/src/index.mjs`; Lambda uses only the `sub` verified by the API Gateway JWT authorizer:

```javascript
const identity = (event) => {
  const claims = event.requestContext?.authorizer?.jwt?.claims;
  if (!claims?.sub) throw new HttpError(401, "Authentication is required");
  return { sub: claims.sub, email: claims.email?.toLowerCase() ?? claims.username ?? claims.sub };
};
```

## Verification evidence

The signed-in document-list API returned HTTP `200`, and the cross-account request for an unshared document returned HTTP `404`; see section 5.7.2. The saved category and description are visible on the document detail page, and document records are shown in DynamoDB; see sections 5.8.2 and 5.4.1. These existing screenshots are reused instead of duplicating them here. A separate Lambda console test-event invocation was not captured.

## Expected result

Metadata handlers operate on the correct user-scoped records.

## Troubleshooting

- Check case-sensitive key names, index configuration, and the sub from the token.
- Avoid full-table scans for regular My Documents queries.
