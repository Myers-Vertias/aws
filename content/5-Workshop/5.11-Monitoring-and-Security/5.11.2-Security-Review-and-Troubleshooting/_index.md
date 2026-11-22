---
title: "Review Security and Troubleshoot Failures"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.11.2 </b> "
---

{{% notice info %}}
S3 Block Public Access and SSE-S3 evidence is recorded in section 5.5.1. The IAM permissions screenshot in section 5.6.1 confirms the Lambda logging and project runtime policies were attached to the execution role.
{{% /notice %}}

## Objective

Follow a consistent diagnostic path and verify private storage and least-privilege IAM.

## Implementation steps

Follow the steps in order. Read the action, its explanation, and the expected check before moving to the next step.


### Step 1: Start from the CloudFormation stack to identify resources

Start from the CloudFormation stack to identify resources and Region; compare API routes/authorizer, Lambda functions, DynamoDB tables/indexes, and Cognito Pool/Client with the template.

### Step 2: In S3 → Permissions, review Block Public Access, bucket

In S3 → Permissions, review Block Public Access, bucket policy, and CORS; in Properties, review SSE-S3. The bucket stays private; CORS does not grant object access.

### Step 3: In the IAM role, compare each Action/Resource with

In the IAM role, compare each Action/Resource with `backend/iam/lambda-runtime-policy.template.json`; review Lambda trust and scoped PassRole. If the lab identity lacks `iam:GetRole`, ask an administrator to review the policy instead of broadening the user's permissions to bypass the denial.

### Step 4: Confirm the frontend contains only public VITE_*

Confirm the frontend contains only public `VITE_*` settings, API Gateway validates JWTs, Lambda reads caller `sub` from claims, and Billing/Budget shows active resources.

## Evidence

The role and attached policies are shown in section 5.6.1. Authenticated API operations and the denied read of an unshared document are recorded in sections 5.7.2 and 5.10.2.

## Expected result

Security checks and troubleshooting evidence are recorded before cleanup.

## Troubleshooting

- Do not solve access failures by making S3 public or granting AdministratorAccess.
- A 401 often relates to authentication and a 403 to authorization; verify the real response.
