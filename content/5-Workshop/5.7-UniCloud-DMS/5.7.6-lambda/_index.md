---
title: "Configure IAM and Implement Lambda"
date: 2026-10-16
weight: 6
chapter: false
pre: " <b> 5.7.6 </b> "
---

## Objective

Create least-privilege execution permissions and implement the backend handlers described by the API contract. Do not start by granting every Lambda full access to AWS.

## Step 1 — Create an execution role

1. Open the [IAM console](https://console.aws.amazon.com/iam/).
2. Create an IAM role for the Lambda service.
3. Attach the AWS-managed basic Lambda execution policy for CloudWatch logging.
4. Add a customer policy limited to the workshop's specific DynamoDB tables and S3 bucket/prefix. Use only the DynamoDB read/write actions needed by the handlers and S3 actions such as GetObject, PutObject, and DeleteObject where required.
5. Allow access to only the resources created for this workshop. Avoid ***** resource permissions when a specific ARN can be used.
6. Create separate roles for materially different permissions if separate handlers have different security needs.

**[Screenshot placeholder 5.7.6-01 — Lambda execution role and scoped policies]**  
Capture the role and policy summary. Do not include credentials or sensitive account values.

## Step 2 — Implement the backend handlers

Use the supported Node.js runtime selected for the account and bundle required AWS SDK v3 packages with the function code. Keep handlers and shared validation utilities in the project source repository.

Implement the API contract from section 5.7.2. At minimum, the backend must:

- Derive the caller's Cognito sub from the verified authorizer context.
- Validate filename, content type, size, category, tags, and description.
- Query DynamoDB by the appropriate keys/indexes rather than scanning the full table.
- Check ownership or an active share before returning metadata or permitting mutations.
- Generate unique, server-controlled S3 keys and short-lived presigned URLs.
- Verify an uploaded object before completing its metadata record.
- Normalize tags and keep category/status codes language-independent.
- Return machine-readable error codes and suitable HTTP status codes.
- Avoid logging full tokens, signed URLs, sensitive metadata, or file contents.

**[Screenshot placeholder 5.7.6-02 — Handler code and successful test invocation]**  
After implementing a handler, capture its code location and a test result with synthetic data. Redact tokens and signed URLs.

## Step 3 — Implement soft delete and permanent delete safely

Moving a document to Trash should update its metadata with deletedAt while preserving the S3 object. Restore clears deletedAt for an authorized owner. Permanent deletion must verify ownership, require that the document is in Trash according to the chosen business rule, remove the S3 object, and then remove related metadata and share/favorite records as designed.

Consider failure ordering: if S3 deletion succeeds but a later database operation fails, the system must have a documented recovery approach. Do not claim multi-service operations are transactional.

## Step 4 — Implement share authorization

When creating a share, validate the recipient account, reject sharing with oneself, and prevent duplicate shares. Only the owner can create or revoke shares. For every read and download, check whether the caller is the owner or an active viewer. Revoking a share should block new API access and new presigned URL issuance; a URL already issued remains usable until it expires.

## Step 5 — Test each handler

Use Lambda test events with synthetic user identities and document records. Include positive tests and negative tests for missing identity, wrong owner, revoked share, invalid file type, oversized file, missing document, and failed object lookup. Do not use a real user's private documents as test fixtures.

## Expected result

Handlers and execution roles are implemented with narrow permissions and pass their focused tests. API integration is completed in the next module.

## Troubleshooting

- **AccessDenied from DynamoDB/S3:** check the role policy, target ARN, Region, and environment variables.
- **Missing identity claim:** inspect the configured authorizer context; never accept ownerSub from the request body as proof of ownership.
- **Presigned URL upload fails:** check object key, expiration, content type, and S3 CORS.
- **Cold start or import error:** verify the selected runtime and bundled dependencies.
