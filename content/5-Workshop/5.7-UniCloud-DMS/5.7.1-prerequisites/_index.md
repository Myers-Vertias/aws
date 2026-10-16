---
title: "Prerequisites and Cost Awareness"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.7.1 </b> "
---

## Objective

Prepare the development environment and AWS account before creating resources. This step does not create any AWS resources.

## Required tools and access

- An AWS account with permission to create the services described in this workshop. Use an IAM identity or role for daily work; do not create or use root access keys.
- Access to the AWS Billing console. Review current pricing and consider creating a budget alert before deploying resources.
- A supported Node.js LTS release, npm, Git, a code editor, and a modern browser.
- Access to the UniCloud DMS source repository and permission to create a working branch.
- A chosen AWS Region. Use the same Region for Cognito, DynamoDB, Lambda, API Gateway, and S3 unless a specific reason requires otherwise.

{{% notice warning %}}
Some resources and operations may incur charges, including data transfer, stored objects, requests, log ingestion, and hosting. Free-tier eligibility varies by account and service. Review the current AWS pricing pages and your account's billing status before creating resources.
{{% /notice %}}

## Step 1 — Verify the local frontend

Open a terminal in the UniCloud DMS repository and run:

~~~powershell
npm ci
npm run lint
npm run build
npm run dev
~~~

Use the local URL printed by Vite to open the current frontend. Confirm that the existing mock prototype runs before connecting AWS. Do not paste credentials or tokens into source files.

**[Screenshot placeholder 5.7.1-01 — Local frontend running]**  
Capture the application running locally with a representative page visible. Replace this notice with the real screenshot after testing.

## Step 2 — Prepare safe configuration practices

- Use a dedicated development environment and a clear resource-name prefix such as **unicloud-dms-dev**.
- Store frontend configuration in environment variables such as a Vite environment file; commit only a sanitized example file.
- Never place AWS secret access keys, session tokens, private credentials, or full JWTs in React source code or Git.
- Record the selected Region and resource names in a local lab notes file that contains no secrets.

**[Screenshot placeholder 5.7.1-02 — Region and non-secret environment configuration]**  
Capture the selected Region or a sanitized configuration example. Do not expose account-sensitive identifiers unnecessarily.

## Expected result

The frontend builds locally, the tools are available, the intended AWS Region is selected, and the learner understands that resources can incur charges.

## Troubleshooting

- If npm commands fail, verify the Node.js version, repository directory, and dependency installation.
- If the site uses a port other than 5173, use the URL printed by Vite.
- If AWS access is denied, ask the account administrator to grant only the permissions required for the current step rather than attaching broad administrator access.

## Summary

No AWS resources should have been created during this step. Continue to the backend design before creating tables, buckets, or functions.
