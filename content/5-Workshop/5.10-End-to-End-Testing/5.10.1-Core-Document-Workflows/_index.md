---
title: "Test Authentication and Core Document Workflows"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.10.1 </b> "
---

{{% notice info %}}
The images below record some observed results. Distinguish local frontend screenshots from production screenshots; do not infer untested cases from a build result or document list.
{{% /notice %}}

## Objective

Record actual outcomes for the sign-in, metadata, upload, view, and download flows.

## Implementation steps

1. Create/verify a dedicated test user, then test sign-in and sign-out.
2. Upload a harmless sample file and verify both the private S3 object and DynamoDB metadata.
3. List the document, open its detail view, update allowed metadata, and download the file.
4. Test invalid file type/size and missing/expired authentication. Record Pass/Fail only after the test was run.

## Screenshots and remaining evidence

![Screenshot 5.10.1: Active document and browser download history on the production frontend](/images/5-Workshop/5.8-Frontend-Integration/5.8.2-document-download-production.png)

![Screenshot 5.10.1: Uploaded object in the private S3 bucket](/images/5-Workshop/5.8-Frontend-Integration/5.8.2-s3-object-upload.png)

![Screenshot 5.10.1: Saved metadata on the document details page in the local frontend run](/images/5-Workshop/5.8-Frontend-Integration/5.8.2-metadata-update-local.png)

The missing-token case is covered by the HTTP `401` evidence in section 5.7.2.

![Screenshot 5.10.1: Network panel showing HTTP 401 for a request without authentication](/images/5-Workshop/5.7-API-Gateway/5.7.2-unauthenticated-request-401.png)

Selecting a `.md` file was rejected by the frontend, which displayed the supported extensions before upload.

![Screenshot 5.10.1: Unsupported Markdown file rejected by the upload form](/images/5-Workshop/5.10-End-to-End-Testing/5.10.1-invalid-file-extension-rejected.png)

Oversized-file rejection and expired-token behavior were not tested in this workshop run.

## Expected result

The main document workflow has reproducible evidence and recorded outcomes.

## Troubleshooting

- Do not mark an unexecuted test as passed because code compiled.
- Use synthetic sample files rather than personal records.
