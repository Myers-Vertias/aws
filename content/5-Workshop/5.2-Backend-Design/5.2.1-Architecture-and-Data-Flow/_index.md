---
title: "Define the Target Architecture"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.2.1 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Document how identity, API calls, metadata, and file objects flow through the application.

## Implementation steps

1. Use the target flow: React frontend → Cognito → API Gateway → Lambda → DynamoDB and private S3.
2. The API authorizer validates tokens; Lambda checks ownership or active share access for document-specific operations.
3. DynamoDB stores metadata and relationships. S3 stores file bytes. Browser file transfers use short-lived presigned URLs issued only after authorization.
4. Draw the services, request arrows, trust boundaries, and the location of each data type. Update the diagram if the final implementation differs.

## Screenshot placeholder

**[Screenshot placeholder 5.2.1 — Approved UniCloud DMS architecture diagram showing services and request directions.]**

## Expected result

The team has a reviewed target architecture and data-flow diagram before implementation.

## Troubleshooting

- Do not describe the target diagram as a deployed architecture until resources have been created and verified.
- Keep the diagram aligned with the API type and hosting service actually chosen.
