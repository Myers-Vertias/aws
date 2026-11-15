---
title: "Configure the App Client and Test Authentication"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.3.2 </b> "
---

{{% notice info %}}
This page records the deployed implementation and observed results. Any unverified behavior is identified explicitly.
{{% /notice %}}

## Objective

Configure a browser-safe app client and verify the sign-up/sign-in flow after integration.

## Implementation steps

1. Create an app client for a browser single-page application. Do not create a client secret for a public SPA.
2. Record App Client ID and configure only the real local/deployed callback and sign-out URLs if managed login is used.
3. Integrate the selected supported Cognito/AWS Amplify JavaScript authentication APIs: sign-up, confirmation, sign-in, session retrieval, and sign-out.
4. Test with dedicated accounts. Keep tokens out of browser logs, screenshots, and Git.

## Deployed configuration and verification

The deployed public SPA client is `epodlpjbopbdc809elojsad3a`; the template sets `GenerateSecret: false`. The frontend uses Cognito User Pools API calls for registration, email confirmation, sign-in, session refresh, and sign-out. Real owner and recipient accounts were used in the document workflows. The signed-in dashboard and authenticated API result are recorded in section 5.8.2; a separate App Client settings screenshot was not retained.

## Expected result

Real Cognito authentication is working in the frontend. The app client ID and pool ID are in section 5.9.2; no client secret is configured for the browser app.

## Troubleshooting

- A browser SPA must not contain a client secret.
- Redirect URLs must match scheme, host, port, and path exactly.
