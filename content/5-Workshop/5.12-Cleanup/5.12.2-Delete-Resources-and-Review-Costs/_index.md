---
title: "Delete Workshop Resources and Review Costs"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.12.2 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Safely remove resources created for this lab in dependency-aware order.

## Implementation steps

1. Before every deletion, confirm the AWS account, Region, exact resource name, and development prefix. Never delete another project's resources.
2. After saving evidence, remove the workshop Amplify app and API Gateway API if no longer needed, then workshop Lambda functions and dedicated log groups.
3. Empty the S3 bucket; if versioning is enabled, delete all object versions and delete markers before deleting the bucket.
4. Delete the workshop DynamoDB tables and Cognito development User Pool only after testing is finished. Remove workshop-only IAM roles after dependants are gone.
5. Review Billing/Cost Explorer after cleanup; cost data may be delayed and some shared resources may be intentionally retained.

## Screenshot placeholder

**[Screenshot placeholder 5.12.2 — Final resource inventory and billing review, with unrelated account data hidden.]**

## Expected result

Unused lab resources are removed safely and any retained resources are documented.

## Troubleshooting

- Versioned buckets need all versions and delete markers removed before deletion.
- Deleting a Cognito User Pool can permanently remove test accounts. Verify it is the workshop pool.
