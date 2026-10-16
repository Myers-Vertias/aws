---
title: "Create a Cognito User Pool"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.3.1 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Create the user directory used to authenticate UniCloud DMS accounts.

## Implementation steps

1. Open the Amazon Cognito console and create a User Pool in the selected Region.
2. Choose email as the sign-in identifier and configure account verification/recovery for the lab.
3. Use a clear development name such as `unicloud-dms-dev-users`; review the choices before creating the pool.
4. Record the Pool ID and Region in a local note. Do not include user passwords or tokens in documentation.

## Screenshot placeholder

**[Screenshot placeholder 5.3.1 — User Pool sign-in settings and the created pool details.]**

## Expected result

A development User Pool exists in the intended Region.

## Troubleshooting

- If the Region is wrong, correct it before creating dependent resources.
- Never include verification codes or credentials in evidence.
