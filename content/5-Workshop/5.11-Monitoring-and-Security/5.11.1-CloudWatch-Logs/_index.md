---
title: "Inspect CloudWatch Logs"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.11.1 </b> "
---

{{% notice info %}}
The `dms-dev-documents` and `dms-dev-health` Lambda log groups exist in `ap-southeast-1`. Their retention is set to **Never expire**. A per-request log event has not yet been correlated.
{{% /notice %}}

## Objective

Correlate an API request with its Lambda execution without exposing secrets.

## Implementation steps

1. Open CloudWatch Logs and find the tested function's `/aws/lambda/...` log group.
2. Invoke the function using synthetic test data and locate the log by timestamp/request ID.
3. Correlate the Lambda outcome with the API Gateway response. Use structured logs with request ID, route, status, and latency if enabled.
4. Do not log authorization headers, full JWTs, presigned URLs, file contents, or sensitive personal data.

## Observed log evidence and limitation

![Screenshot 5.11.1: CloudWatch log groups for the dms-dev document and health Lambdas, with retention shown](/images/5-Workshop/5.11-Monitoring-and-Security/5.11.1-lambda-log-groups.png)

The newest document-function stream contains Lambda runtime `START`, `END`, and `REPORT` records with Lambda request IDs and duration. It does not show the API route or HTTP status, so these records cannot be reliably tied to a specific browser request.

![Screenshot 5.11.1: Lambda runtime START, END, and REPORT records in the document function log stream](/images/5-Workshop/5.11-Monitoring-and-Security/5.11.1-lambda-runtime-events.png)

The handler logs unexpected errors, but does not currently emit route/status details for each handled API request; the earlier 404 request therefore cannot be correlated to a route-level application log event.

## Expected result

The learner can trace an operation to the relevant backend logs.

## Troubleshooting

- If no logs appear, verify the function was invoked and the role can write logs.
- Log storage/ingestion can incur cost; set suitable retention.
