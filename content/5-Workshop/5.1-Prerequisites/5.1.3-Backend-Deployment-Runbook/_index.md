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

### Step 1: Confirm AWS deployment access and select an account

Sign in with the lab account or role provided by your organization, then choose one Region for the workshop. The sample uses `ap-southeast-1` (Singapore). The deployment identity needs access to CloudFormation, Lambda, Cognito, API Gateway, DynamoDB, S3, and CloudWatch, plus `iam:PassRole` limited to this workshop's Lambda execution role. SAM also needs permission to create/use its separate artifact bucket.

If you do not have these permissions, ask the administrator for a lab role or permission set before continuing. Do not use root keys or grant `AdministratorAccess` just to bypass an error.

### Step 2: Review expected costs and create an alert

Open **Billing and Cost Management**, review pricing for the services used, and create an AWS Budget with suitable actual/forecast alerts. A budget sends notifications; it does not stop resources or cap spending.

### Step 3: Configure and verify the AWS CLI profile

Install AWS CLI v2 first. If your organization uses IAM Identity Center, open PowerShell and run these commands in order:

```powershell
aws configure sso --profile unicloud-lab
aws sso login --profile unicloud-lab
aws sts get-caller-identity --profile unicloud-lab
```

When prompted, enter the SSO start URL and SSO Region provided by your administrator, then select the lab account and permission set. If the lab does not use SSO, use an approved IAM profile. The last command must return the intended account ID; stop if it reports a different account. Do not share access keys or copy credentials into the repository.

## 2. Create the Lambda execution role

The SAM template receives an existing role ARN through `LambdaExecutionRoleArn`, so create this role before deployment.


### Step 1: Create a role trusted by Lambda

In the AWS Console, open **IAM → Roles → Create role**. Select **AWS service** as the trusted entity, then choose the **Lambda** use case. This creates the trust relationship that lets Lambda assume the role. If your administrator already provided a dedicated workshop role, use it and skip role creation.

### Step 2: Allow the function to write CloudWatch Logs

Find and attach the AWS-managed policy `AWSLambdaBasicExecutionRole`. It allows the function to write logs; table, bucket, and User Pool access is added separately after deployment.

### Step 3: Name the role and copy its ARN

Give the role a lab-specific name such as `unicloud-dms-lambda-dev`, create it, and open its details to copy the **ARN**. Keep the ARN for the deployment command. Do not attach `AdministratorAccess`.

## 3. Validate and deploy the CloudFormation stack


### Step 1: Validate and build the backend

Open PowerShell in the `backend` directory of the `serverless-document-management` application repository. Run these commands. The first validates the SAM template; the second builds the Lambda package before upload:

```powershell
sam validate --lint
sam build
```


### Step 2: Enter your account ID and role in the deploy command

Replace `<ACCOUNT_ID>` with the ID verified by AWS CLI and `<ROLE_NAME>` with the Lambda role name you created. You may change `unicloud-dms-workshop` to a lab-specific stack name, but keep it consistent in later steps. `--resolve-s3` lets SAM manage an artifact bucket for the deployment package; this is not the document storage bucket. The origins below allow local development. Add the Amplify domain in section 5 after it is available.

```powershell
sam deploy `
     --stack-name unicloud-dms-workshop `
     --region ap-southeast-1 `
     --profile unicloud-lab `
     --resolve-s3 `
     --confirm-changeset `
     --parameter-overrides "ParameterKey=LambdaExecutionRoleArn,ParameterValue=arn:aws:iam::<ACCOUNT_ID>:role/<ROLE_NAME> ParameterKey=AllowedFrontendOrigins,ParameterValue=http://localhost:5173,http://127.0.0.1:5173"
```


### Step 3: Review the change set and wait for completion

Review the change set shown by SAM/CloudFormation. Check the account, stack name, Region, role ARN, and resources to be created. Confirm only if they match your lab. Then open **CloudFormation → Stacks**, select the stack, and wait for `CREATE_COMPLETE`. If it reaches `CREATE_FAILED` or `ROLLBACK_COMPLETE`, open **Events**, read the first failed resource, and resolve the error before retrying.

### Step 4: Save Outputs for the next sections

In the stack details, open **Outputs**. Save `ApiBaseUrl`, `UserPoolId`, `UserPoolClientId`, `DocumentsTableName`, `SharesTableName`, `FavoritesTableName`, and `DocumentsBucketName` in private notes. These values belong to your new stack; do not copy IDs/URLs from evidence screenshots. Never store tokens, passwords, or credentials there.

The stack creates a Cognito User Pool and public app client, an HTTP API with a JWT authorizer, two Lambda functions, three on-demand DynamoDB tables, a private SSE-S3 encrypted bucket, and related permissions/stage resources. The SAM artifact bucket is separate from the application's document bucket.

## 4. Complete the Lambda runtime permissions

After CloudFormation creates the stack, the role can write logs but cannot yet access application data. Complete the runtime policy so Lambda can access only the stack's tables, objects, and User Pool. Do this after deployment because CloudFormation generates the bucket and User Pool IDs.

### Step 1: Fill in the stack values in the sample policy

Open `backend/iam/lambda-runtime-policy.template.json` in the application repository. Replace each placeholder:

- `<REGION>`: the selected Region, such as `ap-southeast-1`.
- `<ACCOUNT_ID>`: the account ID returned by `aws sts get-caller-identity`.
- `<STACK_NAME>`: the name passed to `--stack-name`.
- `<BUCKET_NAME>`: the `DocumentsBucketName` Output.
- `<USER_POOL_ID>`: the `UserPoolId` Output.

The table names follow `<STACK_NAME>-documents`, `<STACK_NAME>-shares`, and `<STACK_NAME>-favorites`. Confirm them in Outputs/Resources rather than guessing. Save the completed policy as a separate file and leave `.template.json` unchanged.

### Step 2: Check the policy scope before attaching it

Open the completed policy and confirm there are no remaining `<...>` placeholders and the JSON is valid. It should allow only the required actions on the three application tables/indexes, the bucket's `users/*/documents/*` prefix, and this User Pool. Do not change a Resource to `*`.

### Step 3: Attach the policy to the Lambda execution role

In the AWS Console, open **IAM → Roles → your workshop Lambda role → Add permissions → Create inline policy → JSON**. Paste the completed policy, choose **Next**, name it `DmsDocumentRuntimeAccess`, and create it. Return to the role and confirm that both `AWSLambdaBasicExecutionRole` and `DmsDocumentRuntimeAccess` appear in its permissions.

If an administrator has already attached this policy to a supplied role, inspect it and do not create a duplicate. Lambda now has the runtime permissions needed to handle document requests.

## 5. Connect the frontend and hosting


### Step 1: Create the local frontend configuration

In the application repository root, copy `.env.example` to `.env.local`. Fill in the variables from CloudFormation Outputs; do not leave any `replace-with-...` values:

| Variable | Value to use |
| --- | --- |
| `VITE_AWS_REGION` | The stack Region |
| `VITE_COGNITO_USER_POOL_ID` | `UserPoolId` Output |
| `VITE_COGNITO_USER_POOL_CLIENT_ID` | `UserPoolClientId` Output |
| `VITE_API_BASE_URL` | `ApiBaseUrl` Output |
| `VITE_S3_BUCKET_NAME` | `DocumentsBucketName` Output |

`VITE_*` values are bundled into the browser, so use only public IDs/URLs. Never put AWS secret keys in them. Do not commit `.env.local`.

### Step 2: Install packages and run the frontend locally

From the application repository root, run `npm ci`, `npm run build`, and `npm run dev` in that order. Open the address printed by Vite, usually `http://localhost:5173`. Stop and restart Vite after changing `.env.local`. If sign-in or API calls fail, check the Output values before proceeding.

### Step 3: Connect the application repository to Amplify

In the AWS Console, open **AWS Amplify → Create new app → Host web app**, connect GitHub, then choose the `serverless-document-management` application repository and `main` branch. Confirm the build command is `npm ci && npm run build` and the output directory is `dist`. Add the five `VITE_*` variables from step 1 using Outputs from the same stack. Do not select the `aws` report repository.

### Step 4: Add the Amplify domain to CORS

After Amplify provides an HTTPS domain, copy the full origin containing only the scheme and hostname, for example `https://main.xxxxx.amplifyapp.com`. Redeploy the SAM stack with this exact origin in `AllowedFrontendOrigins`, retaining local origins if still needed. Do not include a trailing slash. API Gateway and S3 CORS must allow the same origin; CORS does not replace authentication or authorization.

## 6. Review resources and costs

In CloudFormation, use the Resources tab to identify which stack owns each resource. Open the two Lambda log groups in Amazon CloudWatch when operational logs are needed. Monitor Billing and Cost Management/Budgets; Bills and Cost Explorer data may be delayed, and estimates are not final invoices.

## 7. Cleanup procedure

When the project owner decides to end the lab, first confirm the account, Region, and stack name, then save any required information. Delete the application stack through CloudFormation to remove resources managed by that stack. If the S3 bucket contains objects, empty it first; check the SAM artifact bucket and Amplify Hosting separately because they may be outside the application stack. Review independent resources such as the IAM role and budget alert, and remove only lab-specific resources. Recheck charges after billing data has had time to update. These instructions do not mean that resources serving the current application have been deleted.

## References

- SAM source: `backend/template.yaml`.
- Sample Lambda runtime policy: `backend/iam/lambda-runtime-policy.template.json`.
- Frontend environment template: `.env.example`.
- Service-specific instructions: sections 5.3–5.9; cleanup: section 5.12.
