---
title: "Create Shares and Favorites Tables"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.4.2 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Store shares and favorites as user-specific relationships and verify the query paths.

## Implementation steps

1. Create Shares with partition key `documentId` and sort key `recipientSub`; add a recipient GSI for Shared With Me.
2. Create Favorites with partition key `userSub` and sort key `documentId`.
3. Use synthetic records to check that a user queries only their own favorites and shares.
4. Record the final table/index names in the API and Lambda configuration notes. Create only indexes required by actual query patterns.

## Screenshot placeholder

**[Screenshot placeholder 5.4.2 — Shares/Favorites table keys and the recipient index with synthetic examples.]**

## Expected result

Relationship tables support the application's shared and favorite views.

## Troubleshooting

- Extra indexes can add storage/write cost; tie each one to a query requirement.
- Do not use email as a substitute for the Cognito sub in authorization.
