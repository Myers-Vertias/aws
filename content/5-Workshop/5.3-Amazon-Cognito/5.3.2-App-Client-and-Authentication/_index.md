---
title: "Verify the App Client and Authentication"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.3.2 </b> "
---

{{% notice info %}}
This page records the deployed implementation and observed results. Any unverified behavior is identified explicitly.
{{% /notice %}}

## Objective

Verify the browser-safe SPA app client created by SAM and test the sign-up/sign-in flow after integration.

## Implementation steps

Follow the steps in order. Read the action, its explanation, and the expected check before moving to the next step.


### Step 1: Open the stack-created User Pool → App integration → App

Open the stack-created User Pool → App integration → App clients, then select its web client. The template already creates a public SPA client with `GenerateSecret: false`; do not create a client secret.

### Step 2: Read the Client ID from CloudFormation Outputs

Read the Client ID from CloudFormation Outputs. If using managed login, set the exact callback/sign-out URLs; the current frontend calls Cognito User Pools API directly and does not use redirect callbacks.

### Step 3: Put the Pool ID, Client ID, and Region in .env.local as

Put the Pool ID, Client ID, and Region in `.env.local` as described in section 5.8.1. The authentication service calls Cognito for sign-up, email confirmation, sign-in, session refresh, and sign-out.

### Step 4: Keep access/ID tokens in the application session

Keep access/ID tokens in the application session; never write full tokens to logs, screenshots, or Git.

## Deployed configuration and verification

The deployed public SPA client is `epodlpjbopbdc809elojsad3a`; the template sets `GenerateSecret: false`. The frontend uses Cognito User Pools API calls for registration, email confirmation, sign-in, session refresh, and sign-out. Real owner and recipient accounts were used in the document workflows. The signed-in dashboard and authenticated API result are recorded in section 5.8.2; a separate App Client settings screenshot was not retained.

## Expected result

Real Cognito authentication is working in the frontend. The app client ID and pool ID are in section 5.9.2; no client secret is configured for the browser app.

## Troubleshooting

- A browser SPA must not contain a client secret.
- Redirect URLs must match scheme, host, port, and path exactly.
