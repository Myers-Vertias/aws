---
title: "Review Security and Troubleshoot Failures"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.11.2 </b> "
---

{{% notice info %}}
S3 Block Public Access and SSE-S3 evidence is recorded in section 5.5.1. The `dms-dev` user cannot inspect the Lambda execution role because IAM denies `iam:GetRole`; least-privilege policy review remains unverified.
{{% /notice %}}

## Objective

Follow a consistent diagnostic path and verify private storage and least-privilege IAM.

## Implementation steps

1. Check the browser Network panel, then API Gateway route/authorizer, Lambda logs, DynamoDB keys/indexes, S3 CORS/signature, and Cognito issuer/audience.
2. Review S3 Block Public Access and bucket policies; confirm no uploaded object is public.
3. Review each Lambda role for unnecessary wildcard permissions and confirm owner/share authorization on all document routes.
4. Review Billing/Cost Explorer and record any resource intentionally retained after the workshop.

## Observed access-denied evidence

![Screenshot 5.11.2: IAM denied dms-dev permission to inspect the Lambda execution role](/images/5-Workshop/5.11-Monitoring-and-Security/5.11.2-iam-role-review-access-denied.png)

No IAM permissions were changed. The role policy contents and least-privilege scope could not be verified with this session.

## Expected result

Security checks and troubleshooting evidence are recorded before cleanup.

## Troubleshooting

- Do not solve access failures by making S3 public or granting AdministratorAccess.
- A 401 often relates to authentication and a 403 to authorization; verify the real response.
