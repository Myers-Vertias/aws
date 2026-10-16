---
title: "Configure the App Client and Test Authentication"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.3.2 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Configure a browser-safe app client and verify the sign-up/sign-in flow after integration.

## Implementation steps

1. Create an app client for a browser single-page application. Do not create a client secret for a public SPA.
2. Record App Client ID and configure only the real local/deployed callback and sign-out URLs if managed login is used.
3. Integrate the selected supported Cognito/AWS Amplify JavaScript authentication APIs: sign-up, confirmation, sign-in, session retrieval, and sign-out.
4. Test with dedicated accounts. Keep tokens out of browser logs, screenshots, and Git.

## Screenshot placeholder

**[Screenshot placeholder 5.3.2 — App Client configuration and a real sign-in result with sensitive values hidden.]**

## Expected result

The frontend can authenticate test users with Cognito rather than mock auth.

## Troubleshooting

- A browser SPA must not contain a client secret.
- Redirect URLs must match scheme, host, port, and path exactly.
