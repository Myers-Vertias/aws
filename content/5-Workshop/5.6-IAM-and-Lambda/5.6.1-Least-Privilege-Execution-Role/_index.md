---
title: "Create a Least-Privilege Execution Role"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.6.1 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Give each Lambda function only the logging, DynamoDB, and S3 permissions it needs.

## Implementation steps

1. Create a role trusted by Lambda and attach the basic CloudWatch logging policy.
2. Add a policy scoped to the exact table/index ARNs and bucket/prefix needed by each function.
3. Separate roles where permission needs differ significantly. Review every action before attaching the policy.
4. Test permissions through a real invocation; do not fix AccessDenied by granting wildcard access to all resources.

## Screenshot placeholder

**[Screenshot placeholder 5.6.1 — Execution role with resource-scoped policy statements.]**

## Expected result

The Lambda role grants only necessary permissions.

## Troubleshooting

- Verify Region and resource ARN when a valid operation is denied.
- Do not share workshop roles with unrelated production workloads.
