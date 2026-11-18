---
title: "CloudWatch Logs, Metrics, and Alarms"
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

1. In the AWS Console, select `ap-southeast-1` → CloudWatch → Logs → Log groups; open `/aws/lambda/<stack>-documents` or `/aws/lambda/<stack>-health`.
2. Select the log stream around the operation time and read `START`, `END`, and `REPORT`; use time/function request ID to locate an invocation. Do not treat the Lambda request ID as the API Gateway request ID.
3. Set suitable retention in the Retention tab so logs are not stored indefinitely. Lambda publishes `Invocations`, `Errors`, and `Duration` in CloudWatch → Metrics → AWS/Lambda ([Lambda metrics guide](https://docs.aws.amazon.com/lambda/latest/dg/monitoring-metrics.html)).
4. To receive an email when Lambda errors occur, open CloudWatch → Alarms → Create alarm → select `AWS/Lambda` / FunctionName / `Errors`; use Sum, a suitable period, threshold `>= 1`, then select or create an SNS email topic. Recipients must confirm the email subscription ([SNS email alarm guide](https://docs.aws.amazon.com/sns/latest/dg/sns-monitoring-using-cloudwatch.html)). Alarms may incur charges.
5. Do not log authorization headers, full JWTs, presigned URLs, file contents, or sensitive personal data.

## Observed log evidence and limitation

![Screenshot 5.11.1: CloudWatch log groups for the dms-dev document and health Lambdas, with retention shown](/images/5-Workshop/5.11-Monitoring-and-Security/5.11.1-lambda-log-groups.png)

The newest document-function stream contains Lambda runtime `START`, `END`, and `REPORT` records with Lambda request IDs and duration. It does not show the API route or HTTP status, so these records cannot be reliably tied to a specific browser request.

![Screenshot 5.11.1: Lambda runtime START, END, and REPORT records in the document function log stream](/images/5-Workshop/5.11-Monitoring-and-Security/5.11.1-lambda-runtime-events.png)

The handler logs unexpected errors, but does not currently emit route/status details for each handled API request; the earlier 404 request therefore cannot be correlated to a route-level application log event.

## Expected result

The learner can inspect Lambda logs/metrics and receive an alarm when the selected metric crosses its threshold. The recorded environment has Lambda runtime logs and an AWS Budget email alert; no CloudWatch metric alarm or API Gateway access log is recorded.

## Troubleshooting

- If no logs appear, verify the function was invoked and the role can write logs.
- Log storage/ingestion can incur cost; set suitable retention.
