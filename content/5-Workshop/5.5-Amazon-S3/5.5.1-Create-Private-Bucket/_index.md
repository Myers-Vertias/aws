---
title: "Create a Private S3 Bucket"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.5.1 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Create the file bucket with public access blocked and encryption enabled.

## Implementation steps

1. Create a globally unique bucket such as `unicloud-dms-dev-your-unique-suffix` in the selected Region.
2. Keep all Block Public Access settings enabled and keep default encryption on.
3. Do not add public-read ACLs or public bucket policies. Keep uploaded document objects private.
4. Enable versioning only if the application and cleanup procedure are prepared to manage old versions.

## Screenshot placeholder

**[Screenshot placeholder 5.5.1 — Bucket properties showing Region, Block Public Access, and encryption.]**

## Expected result

The S3 bucket is ready and remains private.

## Troubleshooting

- Add a random suffix if the bucket name is already taken.
- Do not disable public access controls to work around upload errors.
