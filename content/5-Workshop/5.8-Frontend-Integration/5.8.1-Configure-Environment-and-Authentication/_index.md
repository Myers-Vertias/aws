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

1. Create `.env.local` with Region `ap-southeast-1`, User Pool `ap-southeast-1_hWIAdC6Xu`, App Client `epodlpjbopbdc809elojsad3a`, and API `https://6wcs1urcm2.execute-api.ap-southeast-1.amazonaws.com`. Git ignores this file; `.env.example` contains placeholders only.
2. The frontend calls the Cognito User Pools JSON API for sign-up, email confirmation, sign-in, session refresh, and sign-out. The User Pool password policy requires at least 12 characters.
3. The API client obtains the Cognito ID token and adds it as `Authorization: Bearer` on API Gateway requests.
4. Vite-exposed `VITE_*` values are public configuration and must not contain AWS secret keys. Restart Vite after changing the environment.

## Verification

The signed-in local dashboard and authenticated document-list requests are shown in section 5.8.2. No separate sign-in screenshot was saved; no password, confirmation code, or token is included in the report.

## Expected result

Real Cognito sign-in and protected API access were verified. `.env.local` is environment-specific and is not included in screenshots or Git.

## Troubleshooting

- Restart Vite after changing .env values.
- Never log or commit full session tokens.
