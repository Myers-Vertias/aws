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

1. Configure public Region, Cognito IDs, and API base URL. Never add secret access keys to frontend variables.
2. Update Cognito callback/sign-out URLs and S3/API CORS with the exact Amplify domain where required.
3. Run deployment and inspect the build log until status is successful.
4. Open the generated HTTPS URL, test login and one protected document operation, then refresh a nested route.

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
