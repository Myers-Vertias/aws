---
title: "Test Authentication and Core Document Workflows"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.10.1 </b> "
---

{{% notice info %}}
The images below record observed results. Each caption identifies whether it came from the local frontend or production.
{{% /notice %}}

## Objective

Record actual outcomes for the sign-in, metadata, upload, view, and download flows.

## Implementation steps

1. Open the frontend configured in section 5.8.1, sign in with the Cognito lab account, and open Documents.
2. Select a supported sample under 20 MiB and upload it. Wait for `Active`; file bytes go directly to the private S3 bucket, while metadata is written to DynamoDB through the API.
3. Open details, update an allowed category/description/tag, and save. Return to the list and reopen details to confirm the saved values; use Download to retrieve the file through a presigned URL.
4. To observe upload limits, select an unsupported extension and a file over 20 MiB. The form rejects the file before upload completes; the API also rejects oversized metadata before issuing a presigned URL.

## Screenshots and remaining evidence

![Screenshot 5.10.1: Active document and browser download history on the production frontend](/images/5-Workshop/5.8-Frontend-Integration/5.8.2-document-download-production.png)

![Screenshot 5.10.1: Uploaded object in the private S3 bucket](/images/5-Workshop/5.8-Frontend-Integration/5.8.2-s3-object-upload.png)

![Screenshot 5.10.1: Saved metadata on the document details page in the local frontend run](/images/5-Workshop/5.8-Frontend-Integration/5.8.2-metadata-update-local.png)

The missing-token case is covered by the HTTP `401` evidence in section 5.7.2.

![Screenshot 5.10.1: Network panel showing HTTP 401 for a request without authentication](/images/5-Workshop/5.7-API-Gateway/5.7.2-unauthenticated-request-401.png)

Selecting a `.md` file was rejected by the frontend, which displayed the supported extensions before upload.

![Screenshot 5.10.1: Unsupported Markdown file rejected by the upload form](/images/5-Workshop/5.10-End-to-End-Testing/5.10.1-invalid-file-extension-rejected.png)

On November 18, 2026, the local upload form rejected the selected file for exceeding its displayed 20 MB limit.

![Screenshot 5.10.1: Local upload form rejects a file over the displayed 20 MB limit](/images/5-Workshop/5.10-End-to-End-Testing/5.10.1-oversized-file-rejected-local.png)

The UI check confirms client-side validation. A separate authenticated request to the deployed `POST /documents/upload-url` supplied a file size of 20 MiB + 1 byte and received HTTP `400` with `File size must be between 1 byte and 20 MiB`. The request contained metadata only; the API rejected it before generating an upload URL, and no file was sent to S3.

![Screenshot 5.10.1: Deployed upload-url API rejects a 20 MiB + 1 byte request with HTTP 400](/images/5-Workshop/5.10-End-to-End-Testing/5.10.1-backend-oversized-file-rejected-400.png)

## Expected result

The main document workflow has reproducible evidence and recorded outcomes.

## Troubleshooting

- Do not mark an unexecuted test as passed because code compiled.
- Use synthetic sample files rather than personal records.
