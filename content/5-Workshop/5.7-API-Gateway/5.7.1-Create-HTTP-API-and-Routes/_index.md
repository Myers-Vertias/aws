---
title: "Create the HTTP API and Routes"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.7.1 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Connect API Gateway routes to their intended Lambda integrations.

## Implementation steps

1. Create an HTTP API in the same Region as Lambda and Cognito.
2. Add integrations/routes matching section 5.2.2 and the service layer. Record the API invoke URL and stage.
3. Ensure API Gateway has permission to invoke the selected Lambda functions.
4. Keep route methods, path parameters, and response expectations consistent with frontend code.

## Screenshot placeholder

**[Screenshot placeholder 5.7.1 — API route list and Lambda integration mapping.]**

## Expected result

Routes point to the correct handlers and the invoke URL is recorded.

## Troubleshooting

- Inspect stage, method, route key, and integration when a request returns 404 or 500.
- Do not create undocumented routes that the frontend never uses.
