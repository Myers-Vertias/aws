---
title: "Create a Private S3 Bucket"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.5.1 </b> "
---

{{% notice info %}}
This page records the deployed implementation and observed results. Any unverified behavior is identified explicitly.
{{% /notice %}}

## Objective

Create the file bucket with public access blocked and encryption enabled.

## Implementation steps

1. Create a globally unique bucket such as `unicloud-dms-dev-your-unique-suffix` in the selected Region.
2. Keep all Block Public Access settings enabled and keep default encryption on.
3. Do not add public-read ACLs or public bucket policies. Keep uploaded document objects private.
4. Enable versioning only if the application and cleanup procedure are prepared to manage old versions.

## Deployment evidence

![Screenshot 5.5.1: S3 bucket with Block all public access enabled](/images/5-Workshop/5.5-Amazon-S3/5.5.1-s3-block-public-access.png)

![Screenshot 5.5.1: Default server-side encryption with S3-managed keys enabled](/images/5-Workshop/5.5-Amazon-S3/5.5.1-s3-default-encryption.png)

The deployed bucket uses server-side encryption with Amazon S3 managed keys (SSE-S3). These screenshots confirm the Singapore Region, Block Public Access, and default encryption. Upload/download through presigned URLs is recorded in section 5.8.2.

## Expected result

The deployed documents bucket remains private with Block Public Access and SSE-S3 enabled.

## Troubleshooting

- Add a random suffix if the bucket name is already taken.
- Do not disable public access controls to work around upload errors.
