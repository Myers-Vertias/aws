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

1. From CloudFormation Outputs, get the Region, `UserPoolId`, `UserPoolClientId`, `ApiBaseUrl`, and `DocumentsBucketName`. At the repository root, copy `.env.example` to `.env.local` and set `VITE_AWS_REGION`, `VITE_COGNITO_USER_POOL_ID`, `VITE_COGNITO_USER_POOL_CLIENT_ID`, `VITE_API_BASE_URL`, and `VITE_S3_BUCKET_NAME` respectively.
2. Run `npm ci`, then `npm run dev`; restart Vite after changing `.env.local`. Git ignores the local file, which must not be committed.
3. The auth service calls Cognito User Pools API for sign-up, email confirmation, sign-in, session refresh, and sign-out. The API client gets the ID token and adds `Authorization: Bearer <token>` to requests.
4. Keep only public IDs/URLs in `VITE_*`; never put AWS secret access keys, credentials, passwords, or tokens in the frontend bundle.

## Verification

The signed-in local dashboard and authenticated document-list requests are shown in section 5.8.2. No separate sign-in screenshot was saved; no password, confirmation code, or token is included in the report.

## Expected result

Real Cognito sign-in and protected API access were verified. `.env.local` is environment-specific and is not included in screenshots or Git.

## Troubleshooting

- Restart Vite after changing .env values.
- Never log or commit full session tokens.
