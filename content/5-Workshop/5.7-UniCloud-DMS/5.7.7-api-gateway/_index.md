---
title: "Configure Amazon API Gateway"
date: 2026-10-16
weight: 7
chapter: false
pre: " <b> 5.7.7 </b> "
---

## Objective

Expose the Lambda handlers through a protected HTTP API. This workshop uses an API Gateway **HTTP API with a JWT authorizer** to keep the REST-style routing concise. If the final project design requires a REST API instead, update this guide and the frontend configuration consistently.

## Step 1 — Create the HTTP API

1. Open the [Amazon API Gateway console](https://console.aws.amazon.com/apigateway/).
2. Create an **HTTP API** and give it a development name such as **unicloud-dms-dev-api**.
3. Add the Lambda integrations and routes defined in section 5.7.2.
4. Use the same Region as the Lambda functions and Cognito User Pool.
5. Create a development stage and enable automatic deployment if that matches the chosen workflow.
6. Record the invoke URL; it will be used as the frontend API base URL.

**[Screenshot placeholder 5.7.7-01 — API routes and Lambda integrations]**  
Capture the route list and integration targets.

## Step 2 — Configure a JWT authorizer

Create a JWT authorizer for the Cognito User Pool:

- **Issuer URL:** https://cognito-idp.<region>.amazonaws.com/<user-pool-id>
- **Audience:** the Cognito App Client ID used by the application.
- Attach the authorizer to all routes that access user documents or change application data.

Replace the placeholders with the real Region and User Pool ID. Verify which token type the frontend sends and confirm it is accepted by the authorizer. The frontend must not send a token for a different pool or client.

Keep any explicitly public route minimal. Do not rely on frontend route guards for API security.

**[Screenshot placeholder 5.7.7-02 — JWT authorizer and protected routes]**  
Capture the issuer, audience, and the protected-route settings, without displaying an actual JWT.

## Step 3 — Configure CORS

Allow only the frontend origins that are needed, including the local development origin and the final Amplify domain. Configure the required methods and headers, including **Authorization** and **Content-Type**. Avoid wildcard origins when credentials or sensitive application responses are involved.

CORS is a browser policy, not an authorization mechanism. Lambda must still verify ownership and share permissions for document-specific operations.

## Step 4 — Test the API

Use the API Gateway test tools where supported or a REST client:

1. Send a protected request without a token; it should be rejected.
2. Send the same request with a valid token; it should reach Lambda.
3. Try a document operation as a user who is neither owner nor active viewer; it should be denied.
4. Check that responses use consistent JSON and meaningful HTTP status codes.
5. Confirm that browser preflight requests succeed from the permitted frontend origin.

**[Screenshot placeholder 5.7.7-03 — Unauthorized and authorized request results]**  
Capture both outcomes using test data. Do not show complete tokens or presigned URLs.

## Expected result

The API has a documented invoke URL, routes are connected to their intended handlers, and protected routes require a token accepted by the JWT authorizer.

## Troubleshooting

- **401 response:** token is missing, expired, issued by another pool, or rejected by the authorizer.
- **403 response:** authentication may have succeeded but the business authorization check denied access.
- **404 or missing route:** inspect stage, method, route key, and invoke URL.
- **CORS error:** compare the browser's origin and preflight headers with the configured CORS settings.
- **500 response:** inspect the matching Lambda CloudWatch logs and the integration configuration.
