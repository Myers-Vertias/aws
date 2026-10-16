---
title: "Monitoring and Troubleshooting"
date: 2026-10-16
weight: 11
chapter: false
pre: " <b> 5.7.11 </b> "
---

## Objective

Use service logs and configuration checks to diagnose failures without exposing secrets or granting unnecessary permissions.

## Step 1 — Inspect Lambda logs

1. Open the [Amazon CloudWatch console](https://console.aws.amazon.com/cloudwatch/).
2. Go to **Logs → Log groups**.
3. Open the log group for the function being tested, commonly named with the **/aws/lambda/** prefix.
4. Find the invocation matching the test timestamp and request ID.
5. Review the error and stack trace, then correlate it with the API Gateway request.

**[Screenshot placeholder 5.7.11-01 — Lambda CloudWatch log for a test request]**  
Capture a useful error or successful execution with tokens, presigned URLs, personal values, and file contents removed.

## Step 2 — Enable and inspect API access logs where appropriate

If API Gateway access logging is configured, use a structured log format that includes request ID, route, status code, and latency. Do not log Authorization headers, cookies, full request bodies containing personal data, or signed URLs.

Set a reasonable log retention period for a student project. Longer retention can increase storage cost. Avoid leaving verbose debug logs enabled in production.

## Step 3 — Follow a diagnostic order

When a browser operation fails, investigate in this order:

1. Browser Network panel: request URL, method, status, CORS/preflight, and response code.
2. API Gateway: route, authorizer result, integration status, and request ID.
3. Lambda: exception, environment variables, timeout, and execution role.
4. DynamoDB: table/index names, keys, item shape, and IAM access.
5. S3: object key, bucket Region, CORS, signed headers, and role permissions.
6. Cognito: User Pool, App Client, token issuer, expiration, and client configuration.

Use synthetic test data. Never paste a complete JWT or presigned URL into a ticket, report, or screenshot.

**[Screenshot placeholder 5.7.11-02 — Sanitized end-to-end diagnostic evidence]**  
Capture related request and log identifiers with sensitive values redacted.

## Step 4 — Review monitoring and costs

Review CloudWatch log groups and the billing dashboard while the lab is running. Keep only the metrics and logs needed to debug and demonstrate the workshop. CloudWatch alarms, dashboards, and log ingestion can have cost implications depending on usage.

## Expected result

A learner can trace a failed request from the browser to API Gateway and Lambda, then to DynamoDB, S3, or Cognito as appropriate, without exposing credentials.

## Troubleshooting

- **No log group appears:** invoke the function and verify its execution role can write logs.
- **Logs have no useful context:** include request IDs and concise structured fields, but never secrets.
- **Request times out:** inspect Lambda timeout, downstream calls, and large payload handling.
- **Repeated authorization errors:** compare the token issuer/audience configuration and the Lambda ownership/share checks.
