---
title: "Deploy with AWS Amplify Hosting"
date: 2026-10-16
weight: 9
chapter: false
pre: " <b> 5.7.9 </b> "
---

## Objective

Host the React/Vite frontend from its application source repository. **Do not connect the workshop documentation repository by mistake**; select the repository that contains the UniCloud DMS frontend and its package.json.

## Step 1 — Connect the frontend repository

1. Open the [AWS Amplify console](https://console.aws.amazon.com/amplify/).
2. Start a new application and choose the Git provider connection workflow.
3. Authorize access to the repository that contains the UniCloud DMS frontend.
4. Choose the intended frontend repository and deployment branch.
5. Confirm the detected framework/build settings.

**[Screenshot placeholder 5.7.9-01 — Selected frontend repository and branch]**  
Capture the repository name and branch. Do not include unrelated private repository information.

## Step 2 — Configure the Vite build

Use the repository's supported package manager and lockfile. For the current npm-based project, the build configuration should be equivalent to:

~~~yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm ci
    build:
      commands:
        - npm run build
  artifacts:
    baseDirectory: dist
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
~~~

Adjust the settings if the source repository uses a different package manager or monorepo path. The build output directory for a standard Vite frontend is **dist**.

## Step 3 — Add frontend environment variables

In the Amplify app's environment-variable settings, configure only the public values needed by the browser, such as Region, Cognito User Pool ID, Cognito App Client ID, and API base URL.

- Use the exact variable names referenced by the frontend code.
- Do not add AWS secret access keys or private credentials.
- Update Cognito callback/sign-out URLs and S3/API CORS origins to include the final deployed domain where required.
- Do not assume that a successful frontend build means the backend endpoints are reachable.

**[Screenshot placeholder 5.7.9-02 — Build settings and sanitized environment variables]**  
Capture the build configuration and variable names while redacting sensitive values.

## Step 4 — Deploy and verify

1. Start the initial deployment.
2. Wait for the build and deployment status to indicate success.
3. Open the generated Amplify URL.
4. Test the login screen and at least one protected document workflow.
5. Confirm API requests use the deployed API URL and the browser console has no configuration or CORS errors.

**[Screenshot placeholder 5.7.9-03 — Successful deployment and live website]**  
Insert a real deployment screenshot after verification.

## Expected result

Amplify serves the frontend over HTTPS from its generated domain and the deployment is linked to the intended frontend repository/branch.

## Troubleshooting

- **Build fails:** inspect the Amplify build log, Node.js/package manager configuration, and lint/build errors.
- **Blank page or refresh gives 404:** confirm the deployed artifact directory and configure SPA routing/rewrites for the app's React Router routes.
- **API does not work on the deployed site:** update the API base URL and exact CORS origins; check the authorizer and browser Network panel.
- **Login redirect fails:** update Cognito callback and sign-out URLs to match the deployed origin exactly.

## Cleanup note

Amplify hosting may incur charges depending on usage and account plan. Remove the Amplify app after testing if it is no longer needed.
