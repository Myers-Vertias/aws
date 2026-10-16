---
title: "Prepare the AWS Account and Cost Controls"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.1.2 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Choose a development account and define a consistent Region and resource naming convention.

## Implementation steps

1. Sign in to the intended AWS account. Do not use root access keys for daily development.
2. Choose one Region for Cognito, DynamoDB, Lambda, API Gateway, and S3, then record it in lab notes.
3. Review current AWS pricing and billing. Consider a budget alert before creating resources; an alert does not automatically stop spending.
4. Use a development prefix such as `unicloud-dms-dev`. Add a unique suffix to globally unique names such as S3 buckets.
5. Use least-privilege IAM identities. Do not solve AccessDenied errors by attaching broad administrator permissions.

## Screenshot placeholder

**[Screenshot placeholder 5.1.2 — Selected AWS Region and budget/cost-control settings, with sensitive account data hidden.]**

## Expected result

Region, resource prefix, and cost-control approach are documented before provisioning.

## Troubleshooting

- Free-tier eligibility varies by account and service; check current pricing instead of assuming resources are free.
- Never capture credentials, passwords, or tokens.
