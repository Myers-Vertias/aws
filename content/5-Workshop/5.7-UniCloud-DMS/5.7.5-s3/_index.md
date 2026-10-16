---
title: "Configure Private Amazon S3 Storage"
date: 2026-10-16
weight: 5
chapter: false
pre: " <b> 5.7.5 </b> "
---

## Objective

Create a private bucket to store document objects. DynamoDB will store metadata and the S3 object key, not the file bytes.

## Step 1 — Create the bucket

1. Open the [Amazon S3 console](https://console.aws.amazon.com/s3/).
2. Choose **Create bucket**.
3. Use a globally unique development bucket name, such as **unicloud-dms-dev-<unique-suffix>**. Bucket names are globally unique; replace the example with a name available to your account.
4. Select the same Region chosen in section 5.7.1.
5. Keep **Block all public access** enabled.
6. Keep default encryption enabled. For this workshop, the default S3-managed encryption is sufficient unless the approved design requires a customer-managed KMS key.
7. Enable versioning only if the implementation and cleanup instructions are prepared to manage object versions.
8. Create the bucket.

**[Screenshot placeholder 5.7.5-01 — Private bucket settings]**  
Capture the bucket's Region, Block Public Access settings, and encryption status.

## Step 2 — Configure CORS for direct uploads

The browser uploads the file directly to S3 using a short-lived presigned PUT URL. S3 CORS must allow the frontend origins that will actually be used, such as the local Vite origin and the final Amplify domain.

In the bucket's **Permissions → Cross-origin resource sharing (CORS)** section, add a configuration similar to the following. Replace the example Amplify origin with the real deployed origin before saving; do not leave a placeholder origin in the final configuration.

~~~json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "PUT", "HEAD"],
    "AllowedOrigins": [
      "http://localhost:5173",
      "https://REPLACE-WITH-YOUR-AMPLIFY-DOMAIN"
    ],
    "ExposeHeaders": ["ETag"],
    "MaxAgeSeconds": 300
  }
]
~~~

Use the narrowest origin list practical for the lab. CORS controls which browser origins may read cross-origin responses; it does **not** replace S3 bucket security or backend authorization.

**[Screenshot placeholder 5.7.5-02 — Saved CORS configuration]**  
Capture the saved CORS rules and redact any unrelated account details.

## Step 3 — Define a safe object-key convention

Use a server-generated key that separates documents by owner and document ID, for example:

~~~text
users/<cognito-sub>/documents/<document-id>/<safe-file-name>
~~~

The actual key must be generated or validated by Lambda. Do not allow the frontend to select arbitrary bucket keys. Avoid including raw email addresses in object keys.

## Step 4 — Plan the presigned URL flow

- Lambda validates the authenticated user, filename, file size, and allowed content type before generating a short-lived PUT URL.
- The frontend sends the file to that URL with the content type used when signing it.
- The completion endpoint verifies that the object exists before the document metadata is finalized.
- The download endpoint checks owner/share permission before creating a short-lived GET URL.
- Never make the bucket or individual uploaded objects public.

## Expected result

A private bucket exists in the chosen Region, public access blocking and encryption are enabled, and CORS names only the required frontend origins.

## Troubleshooting

- **CORS error in the browser:** check the exact origin, allowed method, and allowed request headers.
- **Signature mismatch:** ensure the PUT request uses the same content type and URL returned by Lambda, and do not alter the URL.
- **Access denied:** review the Lambda execution role and bucket policy; do not enable public access as a workaround.
- **Bucket name unavailable:** add a new random suffix and retry.

## Cleanup note

If versioning was enabled, deleting a bucket requires removing object versions and delete markers as well as current objects. Follow the cleanup section instead of assuming an empty current-object listing means the bucket is empty.
