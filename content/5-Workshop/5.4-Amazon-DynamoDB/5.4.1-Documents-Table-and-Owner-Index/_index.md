---
title: "Create the Documents Table and Owner Index"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.4.1 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Create the main metadata table using the approved key design.

## Implementation steps

1. Create `UniCloudDMS-Documents-dev` in the selected Region. Use String partition key `documentId`.
2. Use on-demand capacity for the small variable lab unless the design specifies otherwise; keep encryption enabled.
3. Add the `ownerSub-updatedAt-index` GSI with partition key `ownerSub` and sort key `updatedAt` if this is the approved access pattern.
4. Wait for table and index status to become Active before testing. Avoid full table scans for per-user listing.

## Screenshot placeholder

**[Screenshot placeholder 5.4.1 — Documents table key schema and Active owner index.]**

## Expected result

The main table and owner query index are ready for synthetic test data.

## Troubleshooting

- DynamoDB key names and types are case-sensitive.
- Check index status before debugging query behavior.
