---
title: "Integrate the React Frontend"
date: 2026-10-16
weight: 8
chapter: false
pre: " <b> 5.7.8 </b> "
---

## Objective

Replace the prototype's mock authentication and document data path with the verified Cognito and API Gateway integrations, while preserving the existing interface and business workflows.

## Step 1 — Configure environment variables

Create a local environment file from the repository's sanitized example. Use variable names consistent with the application code, for example:

~~~text
VITE_AWS_REGION=<region>
VITE_COGNITO_USER_POOL_ID=<user-pool-id>
VITE_COGNITO_CLIENT_ID=<app-client-id>
VITE_API_BASE_URL=<api-gateway-invoke-url>
~~~

Replace every placeholder with the values for the development environment. Do not commit a real local environment file if it contains sensitive configuration. Remember that Vite variables are included in the browser bundle; never put secret keys in them.

**[Screenshot placeholder 5.7.8-01 — Sanitized environment configuration]**  
Show variable names and redacted/sample values only. Do not show credentials, tokens, or secrets.

## Step 2 — Integrate Cognito authentication

Use the SDK and configuration finalized in section 5.7.3. Replace mock sign-in, sign-up, session restore, and sign-out behavior with real Cognito operations. Handle email confirmation and authentication errors without displaying raw SDK objects or tokens to the user.

Keep route protection for user experience, but enforce all resource permissions in Lambda.

## Step 3 — Replace mock document services

Update the existing service layer to call the API base URL rather than treating localStorage as the source of truth for documents. Preserve the existing UI contract where practical so that pages need minimal changes.

- Attach the current session token to protected API requests.
- Handle 401 by refreshing or ending the session according to the SDK behavior.
- Handle 403 with a translated access-denied message.
- Map API error codes to the current Vietnamese/English translation dictionaries.
- Keep category and status values stable and language-independent.
- Do not store file bytes or sensitive server responses in localStorage.

**[Screenshot placeholder 5.7.8-02 — A real API request from the frontend]**  
Capture a successful API request and response with personal values, tokens, and sensitive headers redacted.

## Step 4 — Connect upload and download

For upload:

1. Request an upload URL from the API.
2. Upload the file directly to S3 using the returned URL and matching content type.
3. Call the completion endpoint so Lambda can verify the object and save metadata.
4. Update the UI only after a successful completion response; handle abandoned uploads and errors.

For download, request a URL from the API only after the backend has checked owner/share access. Use the returned URL without altering it, and allow it to expire naturally. Do not expose the bucket publicly.

## Step 5 — Verify existing features

Re-test Documents, Document Detail, Favorites, Tags/Categories, Trash, sharing, search/filter, and language switching. Remove mock-only assumptions only after the matching backend route exists and has been tested. Keep a development fallback only if it is intentionally designed and cannot accidentally be mistaken for production data.

## Expected result

The frontend signs in with Cognito and reads/writes document information through the API. Uploads and downloads use private S3 objects and server-issued presigned URLs.

## Troubleshooting

- **Environment changes do not apply:** restart the Vite development server after modifying environment variables.
- **API URL is undefined:** verify the variable prefix and name, then restart Vite.
- **401/403:** inspect the token flow, authorizer configuration, and Lambda authorization logic.
- **S3 upload fails after URL creation:** inspect CORS and ensure the content type matches the signed request.
- **Page still shows mock data:** find any remaining mock service import or localStorage-based document source before changing unrelated components.
