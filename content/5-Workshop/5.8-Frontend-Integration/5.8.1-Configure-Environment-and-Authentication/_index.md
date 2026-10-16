---
title: "Configure Environment and Authentication"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.8.1 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Supply public configuration and connect the frontend to real Cognito authentication.

## Implementation steps

1. Configure the Region, User Pool ID, App Client ID, and API base URL using the exact variable names used by the code.
2. Remember that Vite-exposed variables are public; never put AWS secret keys or credentials in `VITE_*` variables.
3. Replace mock sign-up/sign-in/session/logout behavior with the selected Cognito SDK and attach the intended token to protected requests.
4. Restart Vite after environment changes and test with a dedicated user.

## Screenshot placeholder

**[Screenshot placeholder 5.8.1 — Sanitized environment configuration and a real Cognito sign-in result.]**

## Expected result

The app uses real user sessions and calls the configured API base URL.

## Troubleshooting

- Restart Vite after changing .env values.
- Never log or commit full session tokens.
