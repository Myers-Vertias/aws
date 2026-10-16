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
**Status:** This is a guide framework prepared before backend implementation. Screenshot positions are placeholders; replace them with real images only after performing and verifying each step. Expected results are not deployment evidence.
{{% /notice %}}

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
