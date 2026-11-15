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

1. Read the caller's Cognito `sub` from the API Gateway JWT authorizer claims; return HTTP `401` if it is missing. Ownership is derived from this claim, not a browser-supplied owner ID.
2. List owned documents with `OwnerIndex` (`ownerSub`); fetch a document by `documentId`. A detail request is readable only by its owner or a recipient with a share record. An inaccessible document returns `404`.
3. On upload completion, verify the S3 object with `HeadObject`, check its size and server-generated document ID metadata, then write the document metadata with `ownerSub`, file details, category, description, tags, and timestamps.
4. Metadata updates accept `description`, `category`, and `tags`. The handler validates types and limits, normalizes/deduplicates tags, and conditions the update on the caller being the owner and the upload being active.
5. Return document DTOs and JSON errors with HTTP statuses. The deployed document table key schema and owner index are recorded in section 5.4.1.

## Verification evidence

The signed-in document-list API returned HTTP `200`, and the cross-account request for an unshared document returned HTTP `404`; see section 5.7.2. The saved category and description are visible on the document detail page, and document records are shown in DynamoDB; see sections 5.8.2 and 5.4.1. These existing screenshots are reused instead of duplicating them here. A separate Lambda console test-event invocation was not captured.

## Expected result

Metadata handlers operate on the correct user-scoped records.

## Troubleshooting

- Check case-sensitive key names, index configuration, and the sub from the token.
- Avoid full-table scans for regular My Documents queries.
