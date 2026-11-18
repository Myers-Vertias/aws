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

Cleanup has **not** been performed because the application resources are being retained. An initial billing review was recorded on November 18, 2026: Cost Explorer for November 1–18 displayed `-$0.00` and no visible cost bars; the November Bills page displayed `USD 0.00` with status `Pending`. This is a provisional snapshot, not a finalized invoice. The Amplify app, API Gateway API, Lambda functions, CloudWatch log groups, S3 bucket, DynamoDB tables, Cognito development pool, and related roles remain available for the project. The USD 10 budget is an alert, not a spending cap. No deletion is claimed in this report.

## Read-only CloudFormation inventory

On November 18, 2026, the `dms-dev` stack in `ap-southeast-1` showed `UPDATE_COMPLETE` and 13 resources. The application resources are `DmsUserPool`, `DmsUserPoolClient`, `DocumentsApi` and its default stage, `DocumentsFunction` and its API/CORS permissions, `HealthFunction` and its API permission, `DocumentsTable`, `SharesTable`, `FavoritesTable`, and `DocumentsBucket`. These are project resources and remain in use. The separate `aws-sam-cli-managed-default` stack is not part of the `dms-dev` application stack and is not included in this project inventory.

![CloudFormation dms-dev stack resource inventory, upper rows; account identity redacted](/images/5-Workshop/5.12-Cleanup/5.12.2-cloudformation-dms-dev-resources-top.png)

![CloudFormation dms-dev stack resource inventory, lower rows; account identity redacted](/images/5-Workshop/5.12-Cleanup/5.12.2-cloudformation-dms-dev-resources-bottom.png)

## Implementation steps

1. Confirm the account, Region, stack name, and that the app is no longer needed; save required outputs/data/evidence. Do not start cleanup while the app is in use.
2. The Amplify app and SAM artifact bucket are separate from the application stack. Delete the Amplify app/branch in Amplify Console if hosting is no longer needed.
3. Before deleting the CloudFormation stack, open the document bucket and remove objects; if versioning is enabled, remove all versions and delete markers. Do not confuse it with the SAM artifact bucket.
4. Open CloudFormation → Stacks → select the correct lab stack → Delete. Follow Events to `DELETE_COMPLETE`; the stack manages Cognito, API, Lambda, DynamoDB, and the bucket.
5. Afterwards, review independent resources such as the IAM role, CloudWatch log groups, Amplify, and SAM artifact bucket; remove only lab-specific resources. Revisit Budget and Billing/Cost Explorer after data has had time to update.

Cleanup has not been performed for the `dms-dev` environment because the application is being retained. This is a procedure, not a statement that resources were deleted.

## Evidence status

Billing screenshots from the initial review are recorded in section 5.12.1. No final resource-deletion screenshot exists because cleanup has not been performed. Recheck the account and Region before any future removal, and preserve project resources while the application is still in use.

## Expected result

If cleanup is later approved and completed, record the deleted and retained resources and review delayed cost data. This is a future action, not a result of the current workshop run.

## Troubleshooting

- Versioned buckets need all versions and delete markers removed before deletion.
- Deleting a Cognito User Pool can permanently remove test accounts. Verify it is the workshop pool.
