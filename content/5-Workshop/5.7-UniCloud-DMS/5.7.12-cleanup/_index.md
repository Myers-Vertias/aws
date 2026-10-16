---
title: "Clean Up Workshop Resources"
date: 2026-10-16
weight: 12
chapter: false
pre: " <b> 5.7.12 </b> "
---

## Objective

Remove resources created for this workshop when they are no longer needed, while avoiding deletion of unrelated account resources.

{{% notice warning %}}
Cleanup can permanently delete uploaded sample documents, Cognito test users, tables, and application deployments. Before deleting anything, verify the AWS account, Region, resource name, and development prefix. Never delete a resource that belongs to another project or shared environment.
{{% /notice %}}

## Step 1 — Preserve any required evidence

Before cleanup, capture the final website, test matrix, relevant resource overview, and sanitized log results needed for the report. Do not capture credentials, JWTs, presigned URLs, or private document contents.

**[Screenshot placeholder 5.7.12-01 — Final application and verification summary]**  
Insert evidence of the tested application before deleting its resources.

## Step 2 — Remove hosting and API entry points

1. If no longer needed, delete the Amplify app created for the workshop.
2. Delete the API Gateway API created for this lab.
3. Confirm these are the workshop's resources and not an API/hosting app used by another project.

## Step 3 — Remove Lambda functions and log groups

1. Delete the workshop's Lambda functions after the API no longer depends on them.
2. Review and delete their CloudWatch log groups if the evidence has been saved and retention is no longer required.
3. Keep unrelated log groups intact.

## Step 4 — Empty and delete the S3 bucket

1. Open the correct S3 bucket and verify the name and Region.
2. Download or preserve any required sample evidence before deleting.
3. Empty the bucket. If versioning is enabled, remove all object versions and delete markers.
4. Delete the bucket only after it is empty.

Do not disable public-access blocking as part of cleanup. The bucket should have remained private throughout the workshop.

**[Screenshot placeholder 5.7.12-02 — Workshop bucket emptied and deleted]**  
Capture the actual cleanup result without exposing unrelated buckets.

## Step 5 — Delete DynamoDB tables and Cognito test pool

1. Delete the Documents, Shares, and Favorites tables created for the workshop if they are no longer needed.
2. Delete the Cognito development User Pool only after test users and authentication evidence are no longer required.
3. Confirm the table names and User Pool name before confirming deletion.

## Step 6 — Remove IAM resources created only for this workshop

Delete the workshop-specific IAM policies and roles after their Lambda functions have been removed and no other resource depends on them. Do not delete shared roles, account-wide policies, or roles used by another application.

## Step 7 — Final resource and billing review

- [ ] Amplify app removed if no longer required.
- [ ] API Gateway API removed.
- [ ] Workshop Lambda functions removed.
- [ ] Relevant CloudWatch log groups removed or retention intentionally retained.
- [ ] S3 bucket emptied, including versions if enabled, and deleted if no longer required.
- [ ] Workshop DynamoDB tables removed.
- [ ] Cognito development User Pool removed if no longer required.
- [ ] Workshop-only IAM roles and policies removed safely.
- [ ] AWS Billing/Cost Explorer reviewed for remaining resources or usage.

**[Screenshot placeholder 5.7.12-03 — Final resource review]**  
Capture the final review of resources and billing with unrelated account information redacted.

## Completion

The workshop is complete when the implementation and tests have been documented and resources no longer needed have been cleaned up. Keep the Git repository and report artifacts; AWS resources do not need to remain deployed merely for the written workshop to exist.
