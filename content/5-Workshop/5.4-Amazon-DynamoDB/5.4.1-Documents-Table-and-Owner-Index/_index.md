---
title: "Create the Documents Table and Owner Index"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.4.1 </b> "
---

{{% notice info %}}
This page records the deployed implementation and observed results. Any unverified behavior is identified explicitly.
{{% /notice %}}

## Objective

Create the main metadata table using the approved key design.

## Implementation steps

1. Create `UniCloudDMS-Documents-dev` in the selected Region. Use String partition key `documentId`.
2. Use on-demand capacity for the small variable lab unless the design specifies otherwise; keep encryption enabled.
3. Add the `OwnerIndex` GSI with partition key `ownerSub` and sort key `createdAtDocumentId`, matching the deployed owner-list query.
4. Wait for table and index status to become Active before testing. Avoid full table scans for per-user listing.

## Screenshot and remaining evidence

![Screenshot 5.4.1: Document records in the dms-dev-documents table](/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.1-documents-table-records.png)

The deployed `dms-dev-documents` table uses partition key `documentId` (String), has no sort key, uses on-demand capacity, and is Active.

![Screenshot 5.4.1: dms-dev-documents table key schema and active status](/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.1-documents-table-key-schema.png)

The `OwnerIndex` GSI is **Active**, with `ownerSub` (String) as its partition key and `createdAtDocumentId` (String) as its sort key.

![Screenshot 5.4.1: Active OwnerIndex with ownerSub and createdAtDocumentId keys](/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.1-owner-index-active.png)

## Expected result

The main table and owner query index are ready for synthetic test data.

## Troubleshooting

- DynamoDB key names and types are case-sensitive.
- Check index status before debugging query behavior.
