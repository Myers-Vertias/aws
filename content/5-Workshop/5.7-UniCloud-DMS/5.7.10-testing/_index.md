---
title: "End-to-End Testing and Security Verification"
date: 2026-10-16
weight: 10
chapter: false
pre: " <b> 5.7.10 </b> "
---

## Objective

Verify the complete application with real Cognito users and AWS-backed data. Record actual outcomes; do not mark a test as passed until it has been executed.

## Step 1 — Prepare two test users

Create **User A** and **User B** using the supported sign-up flow. Complete any required verification. Use sample documents that contain no personal or confidential information.

**[Screenshot placeholder 5.7.10-01 — Two test accounts signed in]**  
Capture the application with account identifiers redacted, or use a simple test matrix instead of showing email addresses.

## Step 2 — Test core document operations

Run the following scenarios and record Pass/Fail, the date, and any issue:

| Scenario | Expected result |
|---|---|
| Sign up and sign in | A verified account can sign in and sign out. |
| Create/upload a document | File is stored in private S3 and metadata is stored in DynamoDB after completion verification. |
| List and view a document | The owner sees the document and its metadata. |
| Download | The API checks permission before providing a short-lived URL; file download succeeds for an authorized user. |
| Update metadata | Only the owner can change supported metadata fields. |
| Favorite/unfavorite | Favorites are per user and do not change the document owner. |
| Category and tags | Filters work; stored category/status codes do not change when switching languages. |
| Trash and restore | Soft deletion hides the document from active listings; owner can restore it. |
| Permanent delete | The trashed document and its S3 object are removed according to the documented deletion flow. |
| Language switch | UI labels switch between Vietnamese and English and remain selected after refresh. |

**[Screenshot placeholder 5.7.10-02 — Core document workflow result]**  
Use a genuine test result and mask personal information.

## Step 3 — Verify sharing and authorization

1. Sign in as User A and upload a sample document.
2. Share it with User B using the application's share flow.
3. Sign in as User B and verify the document appears under **Shared With Me**, not **My Documents**.
4. Verify User B can view/download as a Viewer but cannot edit, re-share, move to Trash, or permanently delete the document.
5. Revoke the share as User A. Verify User B cannot obtain new metadata or download URLs.
6. Sign in as a user with no share and verify the document is denied.
7. Test the API directly without a token and with an expired/invalid token.

**[Screenshot placeholder 5.7.10-03 — User B's shared view and denied unauthorized access]**  
Capture separate success/denial evidence. Do not show full JWTs or presigned URLs.

## Step 4 — Record actual results

Use a concise test record:

| Test ID | Actual result | Evidence | Notes |
|---|---|---|---|
| T-01 | Pending until executed | Screenshot placeholder | Replace with actual result |
| T-02 | Pending until executed | Screenshot placeholder | Replace with actual result |
| T-03 | Pending until executed | Screenshot placeholder | Replace with actual result |

Replace **Pending** only after running the corresponding test. If a test fails, record the failure and the fix before retesting.

## Expected result

The documented tests have real outcomes and demonstrate that data isolation and authorization are enforced by the backend, not only by the UI.

## Troubleshooting

- **User B sees User A's document as owned:** review the backend query filter and owner identity derived from the token.
- **Revoked user can still access metadata:** review share lookup and authorization on every relevant endpoint.
- **Old download URL still works briefly:** presigned URLs are bearer URLs until expiration. Use a short expiration and understand that revoking a share cannot retroactively invalidate an already issued URL before it expires.
- **Favorites leak across users:** query the Favorites table using the authenticated userSub.
- **Language switch changes stored category/status:** separate internal codes from localized display labels.
