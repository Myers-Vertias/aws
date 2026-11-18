---
title: "Configure Environment, Deploy, and Verify"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.9.2 </b> "
---

{{% notice info %}}
This page records the deployed implementation and observed results. Any unverified behavior is identified explicitly.
{{% /notice %}}

## Objective

Deploy the frontend and verify its real authentication and API calls.

## Implementation steps

1. In Amplify Console, open the app → Hosting → Environment variables and set the five `VITE_*` values from CloudFormation Outputs; do not add secret access keys. Save to trigger a new deployment.
2. Get the branch HTTPS domain. Update `AllowedFrontendOrigins` in the SAM stack to include this exact origin; the current frontend calls Cognito API directly, so it does not need managed-login callback URLs.
3. In Amplify → branch, follow the build log to success; confirm artifact directory `dist`, build command `npm ci && npm run build`, and the SPA rewrite.
4. Open the production domain in a browser. API and bucket CORS origins must match its scheme/host; API URL, Pool ID, Client ID, and bucket must come from the same stack.

## Deployment evidence and verification

![Screenshot 5.9.2: Production main branch deployed on AWS Amplify](/images/5-Workshop/5.9-Amplify-Hosting/5.9.2-amplify-production-branch.png)

![Screenshot 5.9.2: CloudFormation outputs for the deployed dms-dev stack](/images/5-Workshop/5.9-Amplify-Hosting/5.9.2-cloudformation-stack-outputs.png)

The deployed stack's `AllowedFrontendOrigins` parameter includes the production Amplify origin and the local Vite development origins. The Lambda execution role ARN is redacted in this report copy.

![Screenshot 5.9.2: Deployed frontend origins configured on the dms-dev stack](/images/5-Workshop/5.9-Amplify-Hosting/5.9.2-allowed-frontend-origins.png)

The API health check is recorded in section 5.7.2. Authenticated sign-in, document listing, upload/download, and document operations were exercised in the deployed application; see sections 5.8.2, 5.10.1, and 5.10.2. No separate composite screenshot was captured because the same workflows are already documented in those sections.

## Expected result

The app is available over HTTPS and can complete the verified backend workflow.

## Troubleshooting

- Build success does not guarantee API connectivity; check Network and CORS.
- Callback URLs must match the deployed origin exactly.
