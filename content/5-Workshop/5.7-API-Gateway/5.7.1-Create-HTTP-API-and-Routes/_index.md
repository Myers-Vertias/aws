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

1. Deploy the SAM stack from section 5.1.3; the template creates the HTTP API, stage, and Lambda integrations. Do not create a duplicate API manually.
2. In CloudFormation → stack → Resources, open `DocumentsApi` to reach API Gateway. In Routes, confirm `GET /health`, `ANY /{proxy+}`, and `OPTIONS /{proxy+}`; in Integrations, confirm separate health and document Lambdas.
3. Open Stages, confirm `$default`, and get `ApiBaseUrl` from Outputs. The SAM template configures payload format 2.0 and the Lambda timeout.
4. If routes or handlers change, update `backend/template.yaml`, validate/build/deploy with SAM, and review the change set. Keep routes aligned with `src/services`.

## Deployed routes and integrations

![Screenshot 5.7.1: API Gateway health, proxy, and CORS preflight routes](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.7-API-Gateway/5.7.1-api-routes.png)

The deployed API exposes `GET /health` through its dedicated `dms-dev-health` Lambda integration; document routes are dispatched through `ANY /{proxy+}` to `dms-dev-documents`, and `OPTIONS /{proxy+}` serves CORS preflight. Both Lambda integrations use payload format `2.0` and a 30,000 ms timeout.

![Screenshot 5.7.1: Health route integration details showing the dedicated Lambda function](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.7-API-Gateway/5.7.1-health-lambda-integration-details.png)

![Screenshot 5.7.1: Proxy route integration details showing the Lambda function, payload format, and timeout](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.7-API-Gateway/5.7.1-lambda-integration-details.png)

## Expected result

The deployed `$default` stage routes health checks to `dms-dev-health` and document requests to `dms-dev-documents`; browser/API results are recorded in section 5.7.2 and 5.8.2.

## Troubleshooting

- Inspect stage, method, route key, and integration when a request returns 404 or 500.
- Do not create undocumented routes that the frontend never uses.
