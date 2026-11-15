---
title: "Create a Cognito User Pool"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.3.1 </b> "
---

{{% notice info %}}
This page records the deployed implementation and observed results. Any unverified behavior is identified explicitly.
{{% /notice %}}

## Objective

Create the user directory used to authenticate UniCloud DMS accounts.

## Implementation steps

1. Open the Amazon Cognito console and create a User Pool in the selected Region.
2. Choose email as the sign-in identifier and configure account verification/recovery for the lab.
3. Use a clear development name such as `unicloud-dms-dev-users`; review the choices before creating the pool.
4. Record the Pool ID and Region in a local note. Do not include user passwords or tokens in documentation.

## Deployed configuration and verification

The `dms-dev` stack created the User Pool `ap-southeast-1_hWIAdC6Xu` in `ap-southeast-1`. The SAM template configures email sign-in and verification, verified-email account recovery, and a password policy requiring at least 12 characters with upper/lowercase letters, a number, and a symbol. The deployed pool/client outputs are shown in section 5.9.2. Real sign-up, confirmation, and sign-in were exercised in the application; a separate screenshot of the pool settings was not retained.

## Expected result

The development User Pool is deployed in Singapore and real Cognito authentication was used by the frontend. See sections 5.3.2 and 5.8.2 for the app client and sign-in evidence.

## Troubleshooting

- If the Region is wrong, correct it before creating dependent resources.
- Never include verification codes or credentials in evidence.
