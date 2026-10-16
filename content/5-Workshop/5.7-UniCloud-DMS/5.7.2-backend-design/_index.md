---
title: "Design the Data Model and API Contract"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.7.2 </b> "
---

## Objective

Agree on the data model, API routes, identity rules, and file-upload flow before creating AWS resources. This reduces rework between React, Lambda, API Gateway, DynamoDB, and S3.

## 1. Identity and authorization rules

Use the Cognito user **sub** claim as the stable user identifier. Email is useful for finding a recipient and displaying account information, but should not be the primary authorization key.

Every protected Lambda operation must derive the caller from the validated token context. Do not trust a user ID, owner ID, S3 key, or permission supplied by the browser without checking it server-side.

Define these rules before implementation:

- **Owner:** can manage the document's metadata, share access, move it to Trash, restore it, and permanently delete it.
- **Viewer:** can view and download an explicitly shared, non-trashed document, but cannot edit, re-share, or delete it.
- **Unauthorized user:** receives a suitable 403 or 404 response and must not receive a presigned URL.
- **Revoked share:** must no longer grant access to metadata or future download URLs.

## 2. Proposed DynamoDB model

Use three tables for this workshop to keep ownership, sharing, and per-user favorites explicit. Confirm the names and key design before creating the tables.

| Table | Primary key | Purpose |
|---|---|---|
| Documents | Partition key: **documentId** | File metadata, ownerSub, s3Key, filename, content type, size, category, tags, description, createdAt, updatedAt, deletedAt |
| Shares | Partition key: **documentId**; sort key: **recipientSub** | Share relationship, ownerSub, permission, sharedAt. Add a GSI with recipientSub as partition key and sharedAt as sort key to query Shared With Me. |
| Favorites | Partition key: **userSub**; sort key: **documentId** | A user's favorite relationship and optional createdAt timestamp. |

For the Documents table, add a GSI with **ownerSub** as the partition key and **updatedAt** as the sort key to query documents belonging to one owner. At this workshop's scale, Lambda can filter that owner's query results by deletedAt, category, and tags. Avoid scanning the entire Documents table for normal user requests.

Recommended metadata fields:

- **documentId**, **ownerSub**, **fileName**, **s3Key**
- **contentType**, **sizeBytes**, **category**, **tags**, **description**
- **createdAt**, **updatedAt**, and optional **deletedAt**
- An internal status code such as **active**, **processing**, or **failed**

Use stable category codes such as **other**, **work**, **finance**, **hr**, **technical**, and **meeting**. Store tags in a consistent normalized form, such as lowercase with surrounding whitespace removed. Translate category and status labels only in the UI.

**[Screenshot placeholder 5.7.2-01 — Approved data model]**  
Insert the final ER-style or access-pattern diagram once the table/index design is confirmed.

## 3. Proposed API contract

Use one consistent contract between the frontend and backend. The route names below are the proposed baseline; document any changes before implementation.

| Method | Route | Purpose |
|---|---|---|
| GET | /documents | List the caller's documents |
| POST | /documents/upload-url | Validate upload metadata and issue a short-lived S3 PUT URL |
| POST | /documents/{id}/complete | Verify the uploaded object and persist final metadata |
| GET | /documents/{id} | Get authorized document details |
| PUT | /documents/{id} | Update allowed metadata |
| GET | /documents/{id}/download-url | Authorize and issue a short-lived S3 GET URL |
| POST | /documents/{id}/favorite | Add or remove the caller's favorite |
| GET | /favorites | List the caller's favorite documents |
| GET | /trash | List the caller's trashed documents |
| POST | /documents/{id}/restore | Restore a document owned by the caller |
| DELETE | /documents/{id} | Move an owned document to Trash |
| DELETE | /documents/{id}/permanent | Permanently delete an owned trashed document and its object |
| GET | /shared | List documents explicitly shared with the caller |
| GET/POST | /documents/{id}/shares | List or create shares; creation must validate the recipient |
| DELETE | /documents/{id}/shares/{recipientSub} | Revoke an existing share |

Every route that accesses a document must enforce the same owner/share rules. Permanent deletion is destructive and must be protected by owner authorization and confirmation in the UI.

## 4. File-upload flow

1. The authenticated frontend asks the API for an upload URL, sending file metadata but not the file bytes.
2. Lambda validates file size, content type, caller identity, and permitted category/tag fields. It generates the document ID and S3 key; the browser must not choose an arbitrary object key.
3. The browser uploads directly to S3 using the returned, short-lived PUT URL and matching content type.
4. The frontend calls the completion route. Lambda verifies the object exists, reads its metadata as needed, and writes the final document record.
5. For download, Lambda checks access before issuing a short-lived GET URL.

## 5. Error contract

Return consistent machine-readable error codes, such as **UNAUTHORIZED**, **FORBIDDEN**, **DOCUMENT_NOT_FOUND**, **INVALID_FILE**, **ALREADY_SHARED**, and **USER_NOT_FOUND**. The UI maps codes to Vietnamese or English messages; backend authorization must not depend on the selected language.

**[Screenshot placeholder 5.7.2-02 — API route and permission matrix]**  
Insert the reviewed endpoint list or a table showing which roles may call each route.

## Expected result

The team has a reviewed table/key plan, an endpoint list, a documented upload/download flow, and explicit Owner/Viewer rules. Do not create tables or API routes until these design decisions are understood.
