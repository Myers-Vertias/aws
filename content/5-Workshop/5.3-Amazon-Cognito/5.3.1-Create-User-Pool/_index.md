---
title: "Verify the Cognito User Pool"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.3.1 </b> "
---

{{% notice info %}}
This page records the deployed implementation and observed results. Any unverified behavior is identified explicitly.
{{% /notice %}}

## Objective

Verify the User Pool created by SAM for UniCloud DMS account authentication.

## Implementation steps

Follow the steps in order. Read the action, its explanation, and the expected check before moving to the next step.


### Step 1: Deploy the SAM template using section 5.1.3

Deploy the SAM template using section 5.1.3. The template creates the User Pool, so do not create a second pool manually.

### Step 2: In the AWS Console, select the same Region → Amazon

In the AWS Console, select the same Region → Amazon Cognito → User pools → open the pool owned by the lab's CloudFormation stack. Match its name in the stack's Resources/Outputs.

### Step 3: Open the sign-in configuration and confirm email is the

Open the sign-in configuration and confirm email is the username attribute, email is auto-verified, and account recovery uses a verified email. The template requires a 12-character minimum password with uppercase, lowercase, number, and symbol.

### Step 4: Record the Pool ID and Region in local configuration

Record the Pool ID and Region in local configuration. Do not put passwords, verification codes, or tokens in the report.

## Deployed configuration and verification

The `dms-dev` stack created the User Pool `ap-southeast-1_hWIAdC6Xu` in `ap-southeast-1`. The SAM template configures email sign-in and verification, verified-email account recovery, and a password policy requiring at least 12 characters with upper/lowercase letters, a number, and a symbol. The deployed pool/client outputs are shown in section 5.9.2. Real sign-up, confirmation, and sign-in were exercised in the application; a separate screenshot of the pool settings was not retained.

## Expected result

The development User Pool is deployed in Singapore and real Cognito authentication was used by the frontend. See sections 5.3.2 and 5.8.2 for the app client and sign-in evidence.

## Troubleshooting

- If the Region is wrong, correct it before creating dependent resources.
- Never include verification codes or credentials in evidence.
