---
title: "Create DynamoDB Tables and Indexes"
date: 2026-10-16
weight: 4
chapter: false
pre: " <b> 5.7.4 </b> "
---

## Objective

Create the metadata tables defined in the approved design. Confirm the key names and index names in section 5.7.2 before proceeding; if the design changed, update this guide and the Lambda implementation together.

## Step 1 — Create the Documents table

1. Open the [Amazon DynamoDB console](https://console.aws.amazon.com/dynamodb/).
2. Choose **Create table**.
3. Use a unique development name, for example **UniCloudDMS-Documents-dev**.
4. Set the partition key to **documentId** with type **String**. Do not add a sort key to the base table.
5. Choose on-demand capacity for this small, variable workshop workload unless the account's design requires a different mode.
6. Keep encryption enabled and create the table.
7. In **Indexes**, add a GSI named **ownerSub-updatedAt-index**, with partition key **ownerSub** (String) and sort key **updatedAt** (String). Use this index to query a user's own documents rather than scanning the whole table.

**[Screenshot placeholder 5.7.4-01 — Documents table keys and GSI]**  
Capture the table's key schema and the GSI after it becomes Active.

## Step 2 — Create the Shares table

Create **UniCloudDMS-Shares-dev** with:

- Partition key **documentId** (String)
- Sort key **recipientSub** (String)

Add a GSI named **recipientSub-sharedAt-index** with partition key **recipientSub** and sort key **sharedAt**, both String. The base key supports listing and revoking shares for a document; the GSI supports listing documents shared with a recipient.

**[Screenshot placeholder 5.7.4-02 — Shares table and recipient index]**  
Capture the key schema and GSI configuration.

## Step 3 — Create the Favorites table

Create **UniCloudDMS-Favorites-dev** with partition key **userSub** (String) and sort key **documentId** (String). An item represents one user's favorite relationship with one document. Store createdAt only if it is needed for the UI or ordering.

## Step 4 — Confirm table status and access patterns

Wait until all tables and indexes show **Active**. Review the key design:

- My Documents: query the Documents owner index by the authenticated owner's sub.
- Shared With Me: query the Shares recipient index, then retrieve authorized document metadata.
- Favorites: query by userSub, then retrieve the corresponding documents.
- Trash: query documents for the owner and filter for the presence of deletedAt at this project scale.
- Categories and tags: filter the owner's result set; do not perform a table-wide scan for normal requests.

For this teaching prototype, on-demand billing avoids capacity planning, but it does not mean unlimited free usage. Check the current price list and remove unused tables after the lab.

## Expected result

Three tables and the documented GSIs are Active. No production or personal data has been inserted.

## Troubleshooting

- **Index creation is pending:** wait until the GSI status becomes Active before testing queries.
- **Query returns no items:** check key attribute names and types exactly; DynamoDB keys are case-sensitive.
- **Access denied:** inspect the Lambda execution role in the IAM module. Do not resolve this by granting wildcard permissions to all resources.
