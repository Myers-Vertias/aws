---
title: "Configure Amazon Cognito"
date: 2026-10-16
weight: 3
chapter: false
pre: " <b> 5.7.3 </b> "
---

## Objective

Create a Cognito User Pool for real user accounts and prepare a frontend app client. The user pool replaces the frontend's mock authentication only after the integration code has been implemented and tested.

## Step 1 — Create a User Pool

1. Open the [Amazon Cognito console](https://console.aws.amazon.com/cognito/).
2. Choose **Create user pool**.
3. Configure sign-in with email for this workshop and choose the account-recovery and verification behavior appropriate for the test environment.
4. Enable email verification before allowing an account to be used, if that matches the selected sign-up flow.
5. Give the pool a clear development name, such as **unicloud-dms-dev-users**.
6. Review the selections and create the pool.

**[Screenshot placeholder 5.7.3-01 — User Pool configuration and creation result]**  
Capture the configured sign-in method and the pool details after creation. Hide any sensitive account information.

## Step 2 — Create the app client

1. Open the new User Pool and find **App clients**.
2. Create an app client for a browser-based single-page application.
3. Do **not** create a client secret for a frontend-only SPA. A secret embedded in JavaScript is not confidential.
4. Record the User Pool ID, App Client ID, and Region in a safe local configuration note. These identifiers are configuration, not passwords, but should still be handled thoughtfully.
5. If using a Cognito hosted sign-in page, configure callback and sign-out URLs for the local development origin and the final hosting domain. Only add origins that are actually used.

**[Screenshot placeholder 5.7.3-02 — App Client settings]**  
Capture the app client settings without showing tokens, secrets, or personal account details.

## Step 3 — Plan frontend integration

The recommended React integration uses the AWS Amplify JavaScript authentication APIs, or an equivalently supported Cognito SDK. Add the library only when implementing this step.

Configuration will be supplied through environment variables, for example:

- **VITE_AWS_REGION**
- **VITE_COGNITO_USER_POOL_ID**
- **VITE_COGNITO_CLIENT_ID**

These values are public application configuration, not secret credentials. Never add AWS secret access keys to a Vite variable; all variables prefixed with VITE are exposed to the browser bundle.

Implement sign-up, confirmation, sign-in, sign-out, and session retrieval. Send a Cognito token to API Gateway only after the API authorizer configuration is finalized in a later module.

## Step 4 — Verify authentication

Create two test users through the actual application flow. Confirm sign-up, email verification, sign-in, and sign-out. After frontend integration is complete, confirm that a valid session can provide a token and that the browser does not log or display the full token.

## Expected result

A development User Pool and SPA app client exist, and their non-secret configuration values are recorded. The application is not considered integrated until real sign-up/sign-in is verified through the frontend.

## Troubleshooting

- **Invalid client:** confirm the App Client ID belongs to the selected User Pool and Region.
- **Redirect mismatch:** compare callback/sign-out URLs exactly, including scheme, host, port, and path.
- **Client secret error:** use a browser app client without a client secret.
- **Expired or missing session:** sign in again and inspect the authentication SDK response without printing the raw token.

## Cleanup note

Do not delete the user pool until later workshop testing is complete. Cognito users and related settings may be permanently removed when the pool is deleted.
