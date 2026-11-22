---
title: "Configure Environment and Authentication"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.8.1 </b> "
---

{{% notice info %}}
The frontend is configured for Cognito and the deployed API. Real sign-in and authenticated document requests are recorded in section 5.8.2; `.env.local` is intentionally not shown because it contains environment-specific settings.
{{% /notice %}}

## Objective

Supply public configuration and connect the frontend to real Cognito authentication.

## Implementation steps

Follow the steps in order. Read the action, its explanation, and the expected check before moving to the next step.


### Step 1: Fill in the values from CloudFormation Outputs

Open **CloudFormation → Stacks → workshop stack → Outputs** and keep it available for reference. At the application repository root, copy `.env.example` to `.env.local`. Set `VITE_AWS_REGION` from the stack Region, `VITE_COGNITO_USER_POOL_ID` from `UserPoolId`, `VITE_COGNITO_USER_POOL_CLIENT_ID` from `UserPoolClientId`, `VITE_API_BASE_URL` from `ApiBaseUrl`, and `VITE_S3_BUCKET_NAME` from `DocumentsBucketName`. Do not copy values from old screenshots because every stack has different IDs.

### Step 2: Install packages and start the frontend

Open a terminal at the application repository root, run `npm ci`, then `npm run dev`. Open the local address printed by Vite. After editing `.env.local`, stop Vite with `Ctrl+C` and run `npm run dev` again. Do not commit `.env.local`.

### Step 3: Create a test account and sign in with Cognito

On the application sign-in page, choose sign-up and enter an email account you can access. Use a password that meets the policy in section 5.3.1. Open the email, retrieve the confirmation code, and enter it in the application. After confirmation succeeds, sign in with that email and password. The expected result is an authenticated dashboard. Do not include the confirmation code or token in screenshots or the report.

### Step 4: Confirm the API request is authenticated

Open **Developer Tools → Network**, refresh the Documents page, and select the `GET /documents` request. It should return `200` and show the account's document list. If it returns `401`, sign out and back in. If the API cannot be reached, check `VITE_API_BASE_URL` and CORS in section 5.7.2.

`VITE_*` values are bundled into the frontend, so they must contain only public IDs/URLs. Never put AWS secret access keys, credentials, passwords, or tokens in these variables.

## Verification

The signed-in local dashboard and authenticated document-list requests are shown in section 5.8.2. No separate sign-in screenshot was saved; no password, confirmation code, or token is included in the report.

## Expected result

Real Cognito sign-in and protected API access were verified. `.env.local` is environment-specific and is not included in screenshots or Git.

## Troubleshooting

- Restart Vite after changing .env values.
- Never log or commit full session tokens.
