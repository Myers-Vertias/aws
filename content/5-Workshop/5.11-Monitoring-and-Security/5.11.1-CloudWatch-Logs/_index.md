---
title: "Inspect CloudWatch Logs"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.11.1 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Correlate an API request with its Lambda execution without exposing secrets.

## Implementation steps

1. Open CloudWatch Logs and find the tested function's `/aws/lambda/...` log group.
2. Invoke the function using synthetic test data and locate the log by timestamp/request ID.
3. Correlate the Lambda outcome with the API Gateway response. Use structured logs with request ID, route, status, and latency if enabled.
4. Do not log authorization headers, full JWTs, presigned URLs, file contents, or sensitive personal data.

## Screenshot placeholder

**[Screenshot placeholder 5.11.1 — Sanitized CloudWatch event correlated to a known test request.]**

## Expected result

The learner can trace an operation to the relevant backend logs.

## Troubleshooting

- If no logs appear, verify the function was invoked and the role can write logs.
- Log storage/ingestion can incur cost; set suitable retention.
