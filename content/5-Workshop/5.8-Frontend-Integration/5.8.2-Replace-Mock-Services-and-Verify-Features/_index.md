---
title: "Replace Mock Services and Verify Features"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.8.2 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Move document reads/writes to the backend and test upload/download and all existing features.

## Implementation steps

1. Update the existing service layer to call API Gateway rather than using localStorage as the document source of truth.
2. Map machine-readable API errors to the existing VI/EN translation keys; handle loading, empty results, 401, 403, and network errors.
3. Upload by requesting a URL, PUTing the file to S3, then calling the completion endpoint. Download only after the backend permission check.
4. Connect Favorites, categories/tags, Trash/restore/permanent deletion, and sharing to their API endpoints.
5. Verify that categories/statuses remain stable in storage and the UI language selection survives navigation and refresh.

## Screenshot placeholder

**[Screenshot placeholder 5.8.2 — Real API response and working document features using AWS-backed data, with sensitive values redacted.]**

## Expected result

The current frontend works with the AWS backend without regressing established features.

## Troubleshooting

- A successful build does not prove runtime API calls work; use the browser Network panel.
- Do not make S3 public to work around upload/download errors.
