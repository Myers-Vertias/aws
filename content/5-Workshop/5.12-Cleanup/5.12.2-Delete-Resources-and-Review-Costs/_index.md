---
title: "Delete Workshop Resources and Review Costs"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.12.2 </b> "
---

{{% notice info %}}
This page both explains the cleanup procedure and records the project's actual AWS cleanup on November 19, 2026. The screenshots show the actions performed and the AWS console states observed afterwards.
{{% /notice %}}

## Objective

Safely remove resources created for this lab in dependency-aware order.

## Cleanup scope

The procedure below uses resource names from the workshop environment. For another environment, confirm the AWS account, Region, and resource names first. Deleting a CloudFormation stack applies only to resources managed by that stack. Amplify, CloudWatch alarms/log groups, SNS, IAM, the AWS Budget, and SAM artifacts are handled separately. Screenshots on this page come from the project's cleanup and document the corresponding actions and results.

An initial billing review was recorded on November 18, 2026: Cost Explorer for November 1–18 displayed `-$0.00` and no visible cost bars; the November Bills page displayed `USD 0.00` with status `Pending`. This is a provisional snapshot, not a finalized invoice. The USD 10 budget is an alert, not a spending cap.

## Project resources

The `dms-dev` stack uses logical IDs such as `DmsUserPool`, `DmsUserPoolClient`, `DocumentsApi`, `DocumentsFunction`, `HealthFunction`, DynamoDB tables, and `DocumentsBucket`. The inventory screenshots record this stack's resources before cleanup.

![CloudFormation dms-dev stack resource inventory, upper rows; account identity redacted](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-cloudformation-dms-dev-resources-top.png)

![CloudFormation dms-dev stack resource inventory, lower rows; account identity redacted](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-cloudformation-dms-dev-resources-bottom.png)

## Resource cleanup procedure

1. Perform cleanup only after recording the demo and when the application no longer needs to run. Save source code, reports, and any required data locally. Deleting the stack stops the backend and may permanently remove application data.
2. Open **CloudFormation → Stacks**, confirm the AWS account and `ap-southeast-1` Region, select `dms-dev`, and choose **Delete**. Check the stack name and deletion impact in the confirmation dialog before proceeding.

   ![CloudFormation confirmation dialog for deleting dms-dev](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-delete-stack-confirmation.png)

3. If Events reports `DELETE_FAILED` because of `DocumentsBucket`, open the bucket listed in the stack's **Resources** tab. From the bucket page, choose **Empty** to remove all contents. For a versioned bucket, remove all object versions and delete markers too. Do not confuse the document bucket with the SAM artifact bucket.

   ![Permanent object deletion confirmation for the users prefix in the document bucket](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-empty-s3-confirmation.png)

4. Wait for S3 to finish and verify no objects remain. Return to CloudFormation and choose **Retry delete** for `dms-dev`. If the bucket is empty, do not select it for retention. Use **Force delete this entire stack** only to finish a stack in `DELETE_FAILED`; AWS may retain resources that still cannot be deleted, so inspect for leftovers afterwards.
5. Wait for CloudFormation to refresh and confirm `dms-dev` no longer appears among active stacks. Check Events or deleted stacks if the final state needs confirmation.

   ![CloudFormation state after deleting the dms-dev stack](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-stack-list-after-delete.png)

6. To remove the separately hosted frontend, open **AWS Amplify → All apps** and select the `serverless-document-management` app.

   ![AWS Amplify app list showing serverless-document-management](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-amplify-all-apps.png)

7. Open **App settings → General settings**, verify the app name, and choose **Delete app**. In the confirmation dialog, enter `delete` and confirm. This action cannot be undone.

   ![Amplify app General settings page with the Delete app control](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-amplify-general-settings.png)

8. Wait for the success notice. AWS may report that backend resources associated with branches have been queued for deletion; verify that the app no longer appears in the Amplify app list.

   ![Amplify success notice confirming app deletion and branch backend cleanup queued](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-amplify-deleted.png)

9. To delete the separately created alarm, open **CloudWatch → Alarms**, select `dms-dev-documents-errors`, then choose **Actions → Delete** and confirm.

   ![Select the dms-dev-documents-errors CloudWatch alarm and open Actions Delete](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-cloudwatch-alarm-delete-menu.png)

   ![CloudWatch notice after deleting the alarm](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-cloudwatch-alarm-deleted.png)

10. To delete the SNS topic created for alarm notifications, open **Amazon SNS → Topics**, select `dms-dev-lambda-errors`, and choose **Delete**. In the confirmation dialog, enter `delete me` and confirm. The topic cannot be recovered, and its subscriptions are deleted with it.

   ![Select the dms-dev-lambda-errors SNS topic for deletion](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-sns-topic-delete-selection.png)

   ![Amazon SNS notice after deleting the topic](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-sns-topic-deleted.png)

11. Remove the application log groups created separately from the stack: open **CloudWatch → Logs → Log Management → Log groups**, find and select `/aws/apigateway/dms-dev-access`, `/aws/lambda/dms-dev-documents`, and `/aws/lambda/dms-dev-health`. Choose **Actions → Delete log group(s)**, review the selected names, and confirm. Do not select log groups unrelated to the application, such as `dc-common-lambda-auto-start` and `dc-common-lambda-auto-stop` in the example screenshots.

   ![Example of selecting the three application log groups and opening the CloudWatch delete menu](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-cloudwatch-log-groups-delete-selection.png)

   The screenshot illustrates the console selections; compare the log group names in your own account before confirming.

   ![CloudWatch after deleting the project log groups, with the two unselected dc-common log groups remaining](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-cloudwatch-log-groups-deleted-example.png)

12. After confirming that the workshop's Lambda functions have been deleted, open **IAM → Roles**, find and select the `dms-lambda-execution` role, then choose **Delete**. Verify the role name in the confirmation dialog before proceeding. IAM roles are account-level resources; do not delete service roles or other roles.

   ![Select the dms-lambda-execution IAM role for deletion](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-iam-role-delete-selection.png)

   ![IAM notice after deleting the dms-lambda-execution role](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-iam-role-deleted-example.png)

13. To remove the SAM artifact bucket, match the Physical ID `aws-sam-cli-managed-default-samclisourcebucket-...` to the `SamCliSourceBucket` resource in the `aws-sam-cli-managed-default` stack. After emptying the bucket, open **Amazon S3 → Buckets**, select this exact bucket, choose **Delete**, enter its exact name, and confirm. The suffix varies by account, so always match the actual Physical ID.

   ![Confirmation page for deleting the SAM artifact bucket after entering its name](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-sam-artifact-bucket-delete-confirmation.png)

   ![S3 console after deleting the SAM artifact bucket](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-sam-artifact-bucket-deleted-example.png)

14. Return to **CloudFormation → Stacks**, select `aws-sam-cli-managed-default`, and choose **Delete stack**. Enter the exact stack name in the confirmation dialog and choose **Delete stack** again.

   ![CloudFormation confirmation dialog for deleting aws-sam-cli-managed-default](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-sam-stack-delete-confirmation.png)

   Wait for CloudFormation to finish and check **Events** or the stack status. When the status is `DELETE_COMPLETE`, stack deletion has finished. If an error occurs, review Events to identify the remaining resource before retrying.

   ![DELETE_COMPLETE status for the SAM stack after deletion](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-sam-stack-delete-complete-example.png)

15. To delete the budget created for the workshop, open **Billing and Cost Management → Budgets**, select `My Monthly Cost Budget` with an amount of `$10.00`, then choose **Actions → Delete** and confirm. Do not select `My-200$-budget` in the example unless you have verified that it belongs to this workshop. A budget sends alerts; it does not cap spending.

   ![Select My Monthly Cost Budget and open Actions Delete](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-budget-delete-selection.png)

   ![Budgets list after deleting the workshop budget, with the unrelated My-200$-budget remaining](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.12-Cleanup/5.12.2-budget-deleted-example.png)
16. Review S3 buckets, CloudFormation stacks, CloudWatch log groups/alarms, SNS topics, Amplify apps, and Billing/Cost Explorer after cleanup. Cost data can be delayed; a Bills page for an open month is not a final invoice.

## Troubleshooting

- If the stack reports `DELETE_FAILED` because a bucket is not empty, empty the correct document bucket, including object versions and delete markers.
- If force delete is used, some resources can be retained. Look for `DELETE_SKIPPED` in the deleted stack resources or inspect the relevant service, then remove leftovers manually when safe.
- Deleting a Cognito User Pool can permanently remove test accounts. Verify it is the workshop pool.
