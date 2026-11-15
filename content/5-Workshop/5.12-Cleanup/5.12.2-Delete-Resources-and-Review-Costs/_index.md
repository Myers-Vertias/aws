---
title: "Delete Workshop Resources and Review Costs"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.12.2 </b> "
---

{{% notice info %}}
This page records the deployed implementation and observed results. Any unverified behavior is identified explicitly.
{{% /notice %}}

## Objective

Safely remove resources created for this lab in dependency-aware order.

## Current status

Cleanup has **not** been performed. The Amplify app, API Gateway API, Lambda functions, CloudWatch log groups, S3 bucket, DynamoDB tables, Cognito development pool, and related roles are retained for the running project. A post-deployment Billing/Cost Explorer review has also not been recorded. The USD 10 budget is an alert, not a spending cap. No deletion is claimed in this report.

## Implementation steps

1. Before every deletion, confirm the AWS account, Region, exact resource name, and development prefix. Never delete another project's resources.
2. After saving evidence, remove the workshop Amplify app and API Gateway API if no longer needed, then workshop Lambda functions and dedicated log groups.
3. Empty the S3 bucket; if versioning is enabled, delete all object versions and delete markers before deleting the bucket.
4. Delete the workshop DynamoDB tables and Cognito development User Pool only after testing is finished. Remove workshop-only IAM roles after dependants are gone.
5. Review Billing/Cost Explorer after cleanup; cost data may be delayed and some shared resources may be intentionally retained.

## Evidence status

No final resource-deletion or post-deployment billing screenshot was captured because cleanup and the billing review were not performed. Recheck the account and region before any future removal, and preserve project resources while the application is still in use.

## Expected result

If cleanup is later approved and completed, record the deleted and retained resources and review delayed cost data. This is a future action, not a result of the current workshop run.

## Troubleshooting

- Versioned buckets need all versions and delete markers removed before deletion.
- Deleting a Cognito User Pool can permanently remove test accounts. Verify it is the workshop pool.
