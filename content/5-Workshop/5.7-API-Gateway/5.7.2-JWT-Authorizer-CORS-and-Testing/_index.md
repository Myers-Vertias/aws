---
title: "Configure JWT Authorization, CORS, and Tests"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.7.2 </b> "
---

{{% notice info %}}
The deployed Cognito JWT authorizer, API Gateway CORS settings, unauthenticated rejection, authenticated document listing, and cross-account document access denial have been verified.
{{% /notice %}}

## Objective

Require valid Cognito tokens and check CORS without confusing it with authorization.

## Implementation steps

Follow the steps in order. Read the action, its explanation, and the expected check before moving to the next step.


### Step 1: Review Auth in backend/template.yaml: the JWT issuer is

Review `Auth` in `backend/template.yaml`: the JWT issuer is built from the Region/User Pool, and the audience is the Cognito web App Client ID. SAM applies the default authorizer to the proxy route.

### Step 2: In API Gateway → API → Authorization/Routes, confirm the

In API Gateway → API → Authorization/Routes, confirm the document proxy requires Cognito JWT. Only `GET /health` and `OPTIONS /{proxy+}` are unauthenticated by design.

### Step 3: Keep AllowedFrontendOrigins limited to local Vite and the

Keep `AllowedFrontendOrigins` limited to local Vite and the actual Amplify domain; the template allows Authorization/Content-Type and the methods used by the app.

### Step 4: Change the template and redeploy with SAM

Change the template and redeploy with SAM; review routes/auth/CORS after the stack update. Do not enable CORS credentials when the frontend does not use cookies.

## Screenshot and remaining evidence

![Screenshot 5.7.2: Deployed API health endpoint returning status ok](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.7-API-Gateway/5.7.2-api-health-check.png)

The `ANY /{proxy+}` route has the Cognito JWT authorizer attached. `GET /health` and `OPTIONS /{proxy+}` are unauthenticated for health checks and CORS preflight, respectively.

![Screenshot 5.7.2: JWT authorizer attached to the document proxy route](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.7-API-Gateway/5.7.2-jwt-authorizer-routes.png)

The authorizer reads the bearer token from the `Authorization` header, uses the deployed Cognito User Pool issuer, and checks the web app client as its audience.

![Screenshot 5.7.2: Cognito JWT authorizer issuer and audience](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.7-API-Gateway/5.7.2-jwt-authorizer-details.png)

API Gateway CORS allows the local Vite origins and production Amplify origin, the required HTTP methods and `authorization`/`content-type` headers, with a 600-second max age and credentials disabled.

![Screenshot 5.7.2: API Gateway CORS configuration for local and Amplify origins](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.7-API-Gateway/5.7.2-api-cors-config.png)

The deployed `AllowedFrontendOrigins` parameter is also shown in section 5.9.2.

An unauthenticated `GET /documents` request returned `{"message":"Unauthorized"}`.

![Screenshot 5.7.2: Unauthenticated GET documents request rejected by the API](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.7-API-Gateway/5.7.2-unauthenticated-request.png)

The Network panel confirmed this unauthenticated request returned HTTP `401`.

![Screenshot 5.7.2: Network panel showing HTTP 401 for the unauthenticated documents request](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.7-API-Gateway/5.7.2-unauthenticated-request-401.png)

The signed-in local frontend made two `GET /documents?view=owned&status=active` requests; both returned HTTP `200` and the document list showed `Konoka.png`.

![Screenshot 5.7.2: Authenticated document-list requests returned HTTP 200](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.7-API-Gateway/5.7.2-authenticated-documents-200.png)

Signed in as the other Cognito user, a direct request for the owner's unshared document returned HTTP `404`; the UI displayed “Document not found.” The same request appeared twice in Network, both with status `404`. The API intentionally uses not-found responses for documents outside the caller's access to avoid confirming whether they exist.

![Screenshot 5.7.2: Logged-in recipient receives HTTP 404 when requesting an unshared document](https://raw.githubusercontent.com/Myers-Vertias/aws/main/static/images/5-Workshop/5.7-API-Gateway/5.7.2-authenticated-unshared-document-404.png)

## Expected result

Protected routes reject missing/invalid tokens and Lambda denies unauthorized resource access.

## Troubleshooting

- 401 usually concerns authentication; 403 usually concerns authorization—check the actual response.
- CORS permits browser cross-origin calls but does not grant access to a document.
