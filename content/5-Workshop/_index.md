---
title: "Workshop"
date: 2026-10-16
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Workshop: Building UniCloud DMS on AWS

This workshop guides learners from the existing React/Vite frontend to an AWS-backed document-management application.

{{% notice warning %}}
**Implementation status (15 November 2026):** The `dms-dev` application is deployed in `ap-southeast-1`, and the React frontend is available through Amplify. Real Cognito sign-in, authenticated API access, S3 upload/download, document metadata, Favorites, Trash/restore, and two-user sharing have been observed.
{{% /notice %}}

## Completed and verified

- Created the Cognito User Pool and public web app client; signed up and signed in with real Cognito accounts.
- Deployed the HTTP API, Lambda functions, private S3 bucket, and three DynamoDB tables. The tables and their access indexes are recorded in sections 5.4.1 and 5.4.2.
- Verified authenticated document-list requests (`200`), an unauthenticated request (`401`), and a logged-in second user requesting an unshared document (`404`).
- Verified upload, metadata persistence, download, Favorites, Trash/restore, sharing, recipient visibility/download, category filtering, tag search, and Vietnamese/English interface selection. Evidence is distributed across sections 5.7.2, 5.8.2, and 5.10.
- Configured the S3 bucket as private with default SSE-S3 encryption and added browser CORS rules for local and Amplify origins.

## Current operational notes

- The local upload form and deployed upload API both rejected a file over the 20 MiB limit; section 5.10.1 includes the API response for a `20 MiB + 1 byte` request (`400`).
- The IAM screenshots confirm that the Lambda role had `AWSLambdaBasicExecutionRole` and `DmsDocumentRuntimeAccess` attached. The runtime policy source scopes permissions to project resources, and protected document operations were exercised.
- The deployed architecture diagram and data-model design are included. Billing was captured on November 18, 2026; the November bill was still pending at that time and was not a final invoice.
- AWS project resources were cleaned up on November 19, 2026. The screenshots in sections 5.12.1 and 5.12.2 show the actual cleanup steps and resulting console states, while also serving as a step-by-step guide.

## Workshop contents

- [5.1 Prerequisites](5.1-Prerequisites/)
- [5.2 Backend Design](5.2-Backend-Design/)
- [5.3 Amazon Cognito](5.3-Amazon-Cognito/)
- [5.4 Amazon DynamoDB](5.4-Amazon-DynamoDB/)
- [5.5 Amazon S3](5.5-Amazon-S3/)
- [5.6 IAM and AWS Lambda](5.6-IAM-and-Lambda/)
- [5.7 Amazon API Gateway](5.7-API-Gateway/)
- [5.8 Frontend Integration](5.8-Frontend-Integration/)
- [5.9 AWS Amplify Hosting](5.9-Amplify-Hosting/)
- [5.10 End-to-End Testing](5.10-End-to-End-Testing/)
- [5.11 Monitoring and Security](5.11-Monitoring-and-Security/)
- [5.12 Cleanup](5.12-Cleanup/)

## General principles

- Use one AWS Region unless there is a documented reason not to.
- Keep S3 private and check backend authorization before issuing file URLs.
- Do not include credentials, full JWTs, or presigned URLs in screenshots or Git.
- Mark tests as passed only after actual execution, and clean up resources after evidence is saved.
