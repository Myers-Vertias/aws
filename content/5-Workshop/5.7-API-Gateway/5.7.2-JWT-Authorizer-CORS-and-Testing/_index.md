---
title: "Configure JWT Authorization, CORS, and Tests"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.7.2 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Require valid Cognito tokens and check CORS without confusing it with authorization.

## Implementation steps

1. Configure a JWT authorizer with the Cognito issuer URL and app client ID as the audience.
2. Attach the authorizer to every route that reads or changes user documents.
3. Allow only the required local and deployed frontend origins, methods, and Authorization/Content-Type headers.
4. Test requests without a token, with a valid token, and as a logged-in user who lacks access to a document. Record status codes and request IDs.

## Screenshot placeholder

**[Screenshot placeholder 5.7.2 — JWT authorizer, CORS settings, and examples of denied/authorized requests.]**

## Expected result

Protected routes reject missing/invalid tokens and Lambda denies unauthorized resource access.

## Troubleshooting

- 401 usually concerns authentication; 403 usually concerns authorization—check the actual response.
- CORS permits browser cross-origin calls but does not grant access to a document.
