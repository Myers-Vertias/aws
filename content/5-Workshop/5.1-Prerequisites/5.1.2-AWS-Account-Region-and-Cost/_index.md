---
title: "Prepare the AWS Account and Cost Controls"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.1.2 </b> "
---

{{% notice info %}}
Perform these steps with your own development account. The project's actual configuration is separated below so readers do not copy identifiers or permissions from another account.
{{% /notice %}}

## Objective

Choose a development account and define a consistent Region and resource naming convention.

## Implementation steps

1. Sign in to the intended AWS account. Do not use root access keys for daily development.
2. Choose one Region for Cognito, DynamoDB, Lambda, API Gateway, and S3, then record it in lab notes.
3. Review current AWS pricing and billing. Consider a budget alert before creating resources; an alert does not automatically stop spending.
4. Use a development prefix such as `unicloud-dms-dev`. Add a unique suffix to globally unique names such as S3 buckets.
5. Use least-privilege IAM identities. Do not solve AccessDenied errors by attaching broad administrator permissions.
6. Prefer IAM Identity Center and a dedicated AWS CLI profile for the lab. Grant the permissions required to deploy, with `iam:PassRole` restricted to the project's Lambda execution role. If the class provides an IAM user, use that identity only within its approved scope; do not create long-lived access keys without authorization.
7. Before deployment, verify the target profile and account with `aws sts get-caller-identity --profile <lab-profile>`. Use one Region, such as `ap-southeast-1`, for Cognito, API Gateway, Lambda, DynamoDB, and S3.
8. Create an AWS Budget suitable for the lab, add actual/forecast alerts, and verify the notification recipient. Alerts do not cap spending; review Billing regularly.

## Configuration of the deployed project environment

The recorded deployment used a `dms-dev-cli` group with `SignInLocalDevelopmentAccess`, `PowerUserAccess`, and an inline `DmsPassLambdaExecutionRole` policy that restricts passing `dms-lambda-execution` to Lambda. This is the project's environment configuration, not a recommended permission set to copy unchanged. `PowerUserAccess` is broad and does not manage IAM users/groups; prefer a narrower permission set for a new environment. The configured budget is USD 10 per month with actual 85%, actual 100%, and forecast 100% thresholds. Stack `dms-dev` was deployed in `ap-southeast-1`; the SAM-managed artifact bucket is separate from the document bucket.

## Screenshots and remaining evidence

![Screenshot 5.1.2: IAM development group with workshop deployment permissions](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.1-Prerequisites/5.1.2-iam-dev-group-policy.png)

![Screenshot 5.1.2: Monthly budget alert configuration](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.1-Prerequisites/5.1.2-aws-monthly-budget.png)

The AWS console evidence throughout this report shows the selected Region as Asia Pacific (Singapore), `ap-southeast-1`. The budget screenshot above records the USD 10 alert. A separate screenshot combining the Region and budget settings was not captured.

## Expected result

The `dms-dev` development IAM user/group and scoped role-passing policy are documented above. A USD 10 monthly alert exists, and the AWS resources were deployed in Singapore. An initial Billing/Cost Explorer review was captured on November 18, 2026; the November Bills page was still `Pending` at `USD 0.00`, so this is not a final charge amount. A budget alert does not cap spending.

## Troubleshooting

- Free-tier eligibility varies by account and service; check current pricing instead of assuming resources are free.
- Never capture credentials, passwords, or tokens.
