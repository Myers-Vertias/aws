---
title: "Create the HTTP API and Routes"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.7.1 </b> "
---

{{% notice info %}}
This page records the deployed implementation and observed results. Any unverified behavior is identified explicitly.
{{% /notice %}}

## Objective

Connect API Gateway routes to their intended Lambda integrations.

## Implementation steps

1. Create an HTTP API in the same Region as Lambda and Cognito.
2. Add integrations/routes matching section 5.2.2 and the service layer. Record the API invoke URL and stage.
3. Ensure API Gateway has permission to invoke the selected Lambda functions.
4. Keep route methods, path parameters, and response expectations consistent with frontend code.

## Deployed routes and integrations

![Screenshot 5.7.1: API Gateway health, proxy, and CORS preflight routes](/images/5-Workshop/5.7-API-Gateway/5.7.1-api-routes.png)

The deployed API exposes `GET /health` through its dedicated `dms-dev-health` Lambda integration; document routes are dispatched through `ANY /{proxy+}` to `dms-dev-documents`, and `OPTIONS /{proxy+}` serves CORS preflight. Both Lambda integrations use payload format `2.0` and a 30,000 ms timeout.

![Screenshot 5.7.1: Health route integration details showing the dedicated Lambda function](/images/5-Workshop/5.7-API-Gateway/5.7.1-health-lambda-integration-details.png)

![Screenshot 5.7.1: Proxy route integration details showing the Lambda function, payload format, and timeout](/images/5-Workshop/5.7-API-Gateway/5.7.1-lambda-integration-details.png)

## Expected result

The deployed `$default` stage routes health checks to `dms-dev-health` and document requests to `dms-dev-documents`; browser/API results are recorded in section 5.7.2 and 5.8.2.

## Troubleshooting

- Inspect stage, method, route key, and integration when a request returns 404 or 500.
- Do not create undocumented routes that the frontend never uses.
