---
title: "Configure CORS and Presigned URL Transfers"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.5.2 </b> "
---

{{% notice info %}}
The deployed bucket CORS configuration is captured below. The frontend's presigned upload and download flows are recorded in section 5.8.2.
{{% /notice %}}

## Objective

Allow only the frontend origins needed for direct browser upload/download while keeping S3 private.

## Implementation steps

1. Configure S3 CORS for the exact local Vite origin and deployed Amplify origin; allow only required methods and headers.
2. Lambda validates user identity, file size, type, and metadata before returning a short-lived PUT URL with a server-generated key.
3. The browser uploads with the signed content type; a completion endpoint verifies the object before finalizing metadata.
4. For download, Lambda checks owner/active-share access before issuing a short-lived GET URL. Never log or publish the URL.

## Evidence

The S3 CORS configuration allows `GET`, `HEAD`, and `PUT`, with the required authorization, content-type, and signing headers. Its deployed `AllowedFrontendOrigins` parameter, including the Amplify origin, is shown in section 5.9.2. Upload and download results are documented in section 5.8.2.

![Screenshot 5.5.2: Saved S3 CORS configuration](/images/5-Workshop/5.5-Amazon-S3/5.5.2-s3-cors-config.png)

## Expected result

The browser can transfer a test file through presigned URLs without making the bucket public.

## Troubleshooting

- Check origin, method, headers, URL expiration, and signed content type for failures.
- CORS is not authorization; the backend must still verify permissions.
