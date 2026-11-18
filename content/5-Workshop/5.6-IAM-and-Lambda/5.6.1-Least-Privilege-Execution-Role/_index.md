---
title: "Create a Least-Privilege Execution Role"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.6.1 </b> "
---

{{% notice info %}}
This page records the deployed implementation and observed results. Any unverified behavior is identified explicitly.
{{% /notice %}}

## Objective

Create a dedicated Lambda execution role and grant permissions according to the function's actual needs.

## Implementation steps

1. Create the execution role as described in section 5.1.3, with Lambda as the trusted entity and `AWSLambdaBasicExecutionRole` for CloudWatch Logs. Copy its ARN because SAM requires `LambdaExecutionRoleArn`.
2. Deploy the stack to create the table, bucket, and User Pool names. Then replace the placeholders in `backend/iam/lambda-runtime-policy.template.json` with exact ARNs/names from Outputs and attach the runtime policy to the role.
3. Open IAM → Roles → the Lambda role → Permissions to review the policy. Keep permissions scoped to the three DynamoDB tables/indexes, document prefix in S3, and this project's User Pool.
4. The deployment identity needs `iam:PassRole` scoped to this role and Lambda. Do not attach broad IAM permissions to the Lambda role.

## Screenshots

![Screenshot 5.6.1: Lambda role with the basic logging policy](/images/5-Workshop/5.6-IAM-and-Lambda/5.6.1-lambda-basic-execution-policy.png)

![Screenshot 5.6.1: DmsDocumentRuntimeAccess attached to the Lambda role](/images/5-Workshop/5.6-IAM-and-Lambda/5.6.1-lambda-runtime-policy-attached.png)

## Expected result

The captured role permissions page shows `AWSLambdaBasicExecutionRole` and `DmsDocumentRuntimeAccess`. The local policy source is resource-scoped. Protected document operations were subsequently exercised; live policy contents remain unverified because IAM denied `iam:GetRole`.

## Troubleshooting

- Verify Region and resource ARN when a valid operation is denied.
- Do not share workshop roles with unrelated production workloads.
