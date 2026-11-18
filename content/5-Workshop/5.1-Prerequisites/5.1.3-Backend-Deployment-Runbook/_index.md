---
title: "AWS SAM Backend Deployment Runbook"
date: 2026-10-16
weight: 3
chapter: false
pre: " <b> 5.1.3 </b> "
---

## Objective

Deploy the UniCloud DMS backend through a repeatable workflow. AWS SAM transforms `backend/template.yaml` into a CloudFormation stack. The stack creates Cognito, an HTTP API, Lambda, DynamoDB, and S3 resources. Use your own AWS account, IAM role, stack name, and Region.

## 1. Prepare the account and CLI profile

1. Sign in to the assigned lab account and choose one Region for the workshop. The sample project uses `ap-southeast-1` (Singapore).
2. Open Billing and Cost Management, review service pricing, and create an AWS Budget with alerts. A budget alert sends notifications; it does not stop resources.
3. Configure the AWS CLI with IAM Identity Center when available:

   ```powershell
   aws configure sso --profile unicloud-lab
   aws sso login --profile unicloud-lab
   aws sts get-caller-identity --profile unicloud-lab
   ```

   If the lab does not use SSO, use an administrator-approved IAM identity with only the permissions needed. Do not use root access keys, share access keys, or copy credentials into the repository. Confirm that `get-caller-identity` reports the intended account before continuing.

## 2. Create the Lambda execution role

The SAM template receives an existing role ARN through `LambdaExecutionRoleArn`, so create this role before deployment.

1. In the IAM Console, open Roles → Create role. Select an AWS service trusted entity and the Lambda use case.
2. Attach `AWSLambdaBasicExecutionRole` so Lambda can write CloudWatch Logs.
3. Give the role a development name such as `unicloud-dms-lambda-dev` and copy its ARN. For initial stack creation, the logging permission is enough for CloudFormation to create Lambda; do not attach `AdministratorAccess`.

## 3. Validate and deploy the CloudFormation stack

1. Open a terminal in the repository's `backend` directory. Validate the template and build the Lambda package:

   ```powershell
   sam validate --lint
   sam build
   ```

2. Replace `<ACCOUNT_ID>` and `<ROLE_NAME>` in the command before running it. Use a stack name dedicated to the lab. SAM uses a managed artifact bucket to upload the deployment package. The origins below allow local development; add the Amplify origin in a later stack update after the frontend has its domain.

   ```powershell
   sam deploy `
     --stack-name unicloud-dms-workshop `
     --region ap-southeast-1 `
     --profile unicloud-lab `
     --resolve-s3 `
     --confirm-changeset `
     --parameter-overrides "ParameterKey=LambdaExecutionRoleArn,ParameterValue=arn:aws:iam::<ACCOUNT_ID>:role/<ROLE_NAME> ParameterKey=AllowedFrontendOrigins,ParameterValue=http://localhost:5173,http://127.0.0.1:5173"
   ```

3. Review the change set shown by SAM/CloudFormation. Confirm only when the stack name, Region, resources, and expected cost are correct for the lab. In AWS Console → CloudFormation → Stacks, wait for `CREATE_COMPLETE` or `UPDATE_COMPLETE`.
4. Open the stack's Outputs tab and record `ApiBaseUrl`, `UserPoolId`, `UserPoolClientId`, `DocumentsTableName`, `SharesTableName`, `FavoritesTableName`, and `DocumentsBucketName` in private notes. Never store tokens, passwords, or credentials there.

The stack creates a Cognito User Pool and public app client, an HTTP API with a JWT authorizer, two Lambda functions, three on-demand DynamoDB tables, a private SSE-S3 encrypted bucket, and related permissions/stage resources. The SAM artifact bucket is separate from the application's document bucket.

## 4. Complete the Lambda runtime permissions

After CloudFormation creates the resources, use the outputs to fill in the account ID, Region, three table names, Documents bucket, and User Pool ID in `backend/iam/lambda-runtime-policy.template.json`. Attach the completed policy to the Lambda role in IAM. It should grant only required operations on the application tables/indexes, the `users/*/documents/*` object prefix in the bucket, and this Cognito User Pool. This step comes after the first deployment because CloudFormation generates the bucket and User Pool IDs. Then continue with frontend integration.

## 5. Connect the frontend and hosting

1. In the repository root, copy `.env.example` to `.env.local`. Fill in the Region, User Pool ID, App Client ID, API URL, and Documents bucket from CloudFormation Outputs. These are public frontend settings; do not place AWS secret keys in `VITE_*` variables. Do not commit `.env.local`.
2. Run `npm ci`, `npm run build`, and `npm run dev` to serve the frontend locally. Cognito and API operations require the correct Outputs in `.env.local`.
3. Connect Amplify Hosting to the application repository and desired branch. Use `npm ci`, `npm run build`, artifact directory `dist`, and configure the `VITE_*` variables from the stack Outputs.
4. After Amplify provides a domain, update the CloudFormation/SAM `AllowedFrontendOrigins` parameter to include the exact `https://<amplify-domain>` origin, while retaining any local origins still needed. The API and S3 CORS rules must match the actual origin.

## 6. Review resources and costs

In CloudFormation, use the Resources tab to identify which stack owns each resource. Open the two Lambda log groups in Amazon CloudWatch when operational logs are needed. Monitor Billing and Cost Management/Budgets; Bills and Cost Explorer data may be delayed, and estimates are not final invoices.

## 7. Cleanup procedure

When the project owner decides to end the lab, first confirm the account, Region, and stack name, then save any required information. Delete the application stack through CloudFormation to remove resources managed by that stack. If the S3 bucket contains objects, empty it first; check the SAM artifact bucket and Amplify Hosting separately because they may be outside the application stack. Review independent resources such as the IAM role and budget alert, and remove only lab-specific resources. Recheck charges after billing data has had time to update. These instructions do not mean that resources serving the current application have been deleted.

## References

- SAM source: `backend/template.yaml`.
- Sample Lambda runtime policy: `backend/iam/lambda-runtime-policy.template.json`.
- Frontend environment template: `.env.example`.
- Service-specific instructions: sections 5.3–5.9; cleanup: section 5.12.
