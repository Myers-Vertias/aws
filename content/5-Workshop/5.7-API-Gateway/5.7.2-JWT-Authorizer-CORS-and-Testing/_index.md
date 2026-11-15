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

1. Configure a JWT authorizer with the Cognito issuer URL and app client ID as the audience.
2. Attach the authorizer to every route that reads or changes user documents.
3. Allow only the required local and deployed frontend origins, methods, and Authorization/Content-Type headers.
4. Test requests without a token, with a valid token, and as a logged-in user who lacks access to a document. Record status codes and request IDs.

## Screenshot and remaining evidence

![Screenshot 5.7.2: Deployed API health endpoint returning status ok](/images/5-Workshop/5.7-API-Gateway/5.7.2-api-health-check.png)

The `ANY /{proxy+}` route has the Cognito JWT authorizer attached. `GET /health` and `OPTIONS /{proxy+}` are unauthenticated for health checks and CORS preflight, respectively.

![Screenshot 5.7.2: JWT authorizer attached to the document proxy route](/images/5-Workshop/5.7-API-Gateway/5.7.2-jwt-authorizer-routes.png)

The authorizer reads the bearer token from the `Authorization` header, uses the deployed Cognito User Pool issuer, and checks the web app client as its audience.

![Screenshot 5.7.2: Cognito JWT authorizer issuer and audience](/images/5-Workshop/5.7-API-Gateway/5.7.2-jwt-authorizer-details.png)

API Gateway CORS allows the local Vite origins and production Amplify origin, the required HTTP methods and `authorization`/`content-type` headers, with a 600-second max age and credentials disabled.

![Screenshot 5.7.2: API Gateway CORS configuration for local and Amplify origins](/images/5-Workshop/5.7-API-Gateway/5.7.2-api-cors-config.png)

The deployed `AllowedFrontendOrigins` parameter is also shown in section 5.9.2.

An unauthenticated `GET /documents` request returned `{"message":"Unauthorized"}`.

![Screenshot 5.7.2: Unauthenticated GET documents request rejected by the API](/images/5-Workshop/5.7-API-Gateway/5.7.2-unauthenticated-request.png)

The Network panel confirmed this unauthenticated request returned HTTP `401`.

![Screenshot 5.7.2: Network panel showing HTTP 401 for the unauthenticated documents request](/images/5-Workshop/5.7-API-Gateway/5.7.2-unauthenticated-request-401.png)

The signed-in local frontend made two `GET /documents?view=owned&status=active` requests; both returned HTTP `200` and the document list showed `Konoka.png`.

![Screenshot 5.7.2: Authenticated document-list requests returned HTTP 200](/images/5-Workshop/5.7-API-Gateway/5.7.2-authenticated-documents-200.png)

Signed in as the other Cognito user, a direct request for the owner's unshared document returned HTTP `404`; the UI displayed “Document not found.” The same request appeared twice in Network, both with status `404`. The API intentionally uses not-found responses for documents outside the caller's access to avoid confirming whether they exist.

![Screenshot 5.7.2: Logged-in recipient receives HTTP 404 when requesting an unshared document](/images/5-Workshop/5.7-API-Gateway/5.7.2-authenticated-unshared-document-404.png)

## Expected result

Protected routes reject missing/invalid tokens and Lambda denies unauthorized resource access.

## Troubleshooting

- 401 usually concerns authentication; 403 usually concerns authorization—check the actual response.
- CORS permits browser cross-origin calls but does not grant access to a document.
