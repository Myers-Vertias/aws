---
title: "Create Shares and Favorites Tables"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.4.2 </b> "
---

{{% notice info %}}
This page records the deployed implementation and observed results. Any unverified behavior is identified explicitly.
{{% /notice %}}

## Objective

Store shares and favorites as user-specific relationships and verify the query paths.

## Implementation steps

1. Deploy the same SAM stack from section 5.1.3; the template creates both relationship tables, so do not create duplicates manually.
2. In CloudFormation → stack → Resources, select `SharesTable`. Confirm `<stack-name>-shares` has `recipientSub` partition key, `documentId` sort key, and On-demand capacity. In Indexes, confirm `OwnerShareIndex` uses `ownerSub` and `documentIdRecipientSub`.
3. Open `FavoritesTable` and confirm `<stack-name>-favorites` has `userSub` partition key, `documentId` sort key, and On-demand capacity. `DocumentFavoritesIndex` uses `documentId` and `documentIdUserSub` to query favorites by document.
4. Record table/index names from stack outputs/template for Lambda configuration. Cognito `sub` values are used as identity keys; email is not an authorization key.

## Screenshots and remaining evidence

![Screenshot 5.4.2: Favorite records in the dms-dev-favorites table](/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.2-favorites-table-records.png)

![Screenshot 5.4.2: Share records in the dms-dev-shares table](/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.2-shares-table-records.png)

![Screenshot 5.4.2: dms-dev-shares key schema and active table status](/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.2-shares-table-key-schema.png)

![Screenshot 5.4.2: Active OwnerShareIndex on dms-dev-shares](/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.2-shares-owner-index.png)

![Screenshot 5.4.2: dms-dev-favorites key schema and active table status](/images/5-Workshop/5.4-Amazon-DynamoDB/5.4.2-favorites-table-key-schema.png)

The deployed Shares table is Active and uses on-demand capacity. Its primary key is `recipientSub` (partition key) plus `documentId` (sort key). Its active `OwnerShareIndex` uses `ownerSub` as the partition key and `documentIdRecipientSub` as the sort key. The deployed Favorites table is also Active and uses on-demand capacity, with `userSub` as the partition key and `documentId` as the sort key.

## Expected result

Relationship tables support the application's shared and favorite views.

## Troubleshooting

- Extra indexes can add storage/write cost; tie each one to a query requirement.
- Do not use email as a substitute for the Cognito sub in authorization.
