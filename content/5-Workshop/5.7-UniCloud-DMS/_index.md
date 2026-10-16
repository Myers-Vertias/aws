---
title: "Build UniCloud DMS on AWS"
date: 2026-10-16
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

# Building a Serverless Document Management System on AWS

{{% notice info %}}
**Workshop status:** This guide is a prepared implementation framework. The AWS resources, screenshots, and test results must be supplied after the corresponding steps have been completed. Do not treat an expected result as proof of a successful deployment.
{{% /notice %}}

## Overview

UniCloud DMS is a document-management application whose current prototype uses React and Vite. Its frontend includes document browsing and details, Favorites, Tags/Categories, Trash, sharing workflows, authentication mock flows, and a Vietnamese/English interface. This workshop describes how to connect those workflows to real AWS services.

The target architecture uses Amazon Cognito for user authentication, Amazon API Gateway for the REST API, AWS Lambda for business logic and authorization, Amazon DynamoDB for document metadata and relationships, and Amazon S3 for private file objects. AWS Amplify Hosting serves the frontend, while Amazon CloudWatch provides logs for troubleshooting.

## Learning objectives

By completing this workshop, learners should be able to:

- Authenticate users through Amazon Cognito.
- Store file objects privately in S3 and metadata in DynamoDB.
- Implement API operations using API Gateway and Lambda.
- Upload and download objects through short-lived presigned URLs.
- Enforce document ownership and explicit viewer sharing in the backend.
- Connect the existing React frontend to real API endpoints.
- Deploy the frontend and validate the application in Vietnamese and English.
- Inspect logs and clean up resources created for the lab.

## Target architecture

**[Screenshot placeholder 5.7-01 — Final UniCloud DMS architecture diagram]**  
Create or insert the final diagram after the backend design has been approved. It should show Cognito, the React frontend hosted by Amplify, API Gateway, Lambda, DynamoDB, S3, and CloudWatch, with the direction of authenticated requests clearly marked.

Request flow:

1. A user signs in and obtains tokens from Cognito.
2. The frontend sends a valid token with protected API requests.
3. API Gateway validates the token and invokes the appropriate Lambda function.
4. Lambda checks the caller's ownership or sharing permission before returning data or issuing a presigned URL.
5. DynamoDB stores metadata and access relationships; S3 stores the actual file objects.
6. CloudWatch receives logs from Lambda and other configured components.

## Workshop modules

1. [Prerequisites and cost awareness](5.7.1-prerequisites/)
2. [Design the data model and API contract](5.7.2-backend-design/)
3. [Configure Amazon Cognito](5.7.3-cognito/)
4. [Create DynamoDB tables and indexes](5.7.4-dynamodb/)
5. [Configure private Amazon S3 storage](5.7.5-s3/)
6. [Configure IAM and implement Lambda functions](5.7.6-lambda/)
7. [Configure Amazon API Gateway](5.7.7-api-gateway/)
8. [Integrate the React frontend](5.7.8-frontend-integration/)
9. [Deploy with AWS Amplify Hosting](5.7.9-amplify/)
10. [End-to-end testing and security verification](5.7.10-testing/)
11. [Monitoring and troubleshooting](5.7.11-monitoring/)
12. [Clean up workshop resources](5.7.12-cleanup/)

## Screenshot convention

Each module provides unique screenshot IDs. After completing a step, replace the matching placeholder notice with a Markdown image using a real image path, for example:

```markdown
![Cognito app client configuration](/images/5-Workshop/5.7-UniCloud-DMS/5.7.3-02-app-client.png)
```

Save images in the repository's existing image convention. Do not use screenshots containing passwords, access keys, full JWTs, or other secrets. Redact personal data where appropriate.

## Security principles

- Keep the S3 bucket private; do not enable public access for convenience.
- Use the Cognito `sub` claim as the stable internal user identifier. Treat email as an attribute, not the authorization key.
- Enforce authorization in Lambda. Hiding buttons or protecting frontend routes is not a backend security boundary.
- Keep category and status codes language-independent; translate labels only in the UI.
- Check permissions before generating each download URL.
- Give each Lambda execution role only the permissions its function requires.
- Use test accounts and non-sensitive sample documents.

## Completion checklist

- [ ] Cognito registration and login work with real AWS identity.
- [ ] Protected API requests require a valid token.
- [ ] Document metadata is stored in DynamoDB.
- [ ] Files are uploaded and downloaded from private S3 through presigned URLs.
- [ ] Favorites, Tags/Categories, and Trash work with backend data.
- [ ] Sharing between two users works, and revoked access is denied.
- [ ] Vietnamese and English labels remain consistent across pages.
- [ ] The deployed frontend and service logs have been checked.
- [ ] Cleanup steps have been reviewed and executed where appropriate.

This page is a guide framework, not a deployment record. Add verified resource names, real screenshots, tested outputs, and observed issues as implementation proceeds.
