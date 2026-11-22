---
title: "Connect the Frontend Repository and Configure Build"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.9.1 </b> "
---

## Objective

Connect Amplify to the repository containing UniCloud DMS, not the workshop documentation repository.

## Implementation steps

Follow the steps in order. Read the action, its explanation, and the expected check before moving to the next step.


### Step 1: Connect the correct repository in Amplify

In the AWS Console, open **AWS Amplify → Create new app → Host web app**. Choose GitHub and grant repository access if AWS prompts you. Find and select the `serverless-document-management` application repository. The `aws` repository contains the report, not the frontend source to deploy.

### Step 2: Select the main branch and application root

Select the `main` branch. In the build setup, keep the app root at the repository root because `package.json` and `package-lock.json` are there. If Amplify asks about a monorepo, do not enter a subdirectory.

### Step 3: Enter the build command and output directory

Set the build command to `npm ci && npm run build` and the artifact/output directory to `dist`. Vite creates this directory after the build.

To prevent a 404 when refreshing a React Router route directly, open **Hosting → Rewrites and redirects → Manage redirects**. Add a 200 rewrite to `/index.html`. You can use the SPA rule from [AWS Amplify documentation](https://docs.aws.amazon.com/amplify/latest/userguide/redirect-rewrite-examples.html):

```json
[
  {
    "source": "</^[^.]+$|\\.(?!(css|gif|ico|jpg|js|png|txt|svg|woff|woff2|ttf|map|json|webp)$)([^.]+$)/>",
    "status": "200",
    "target": "/index.html",
    "condition": null
  }
]
```

This rule sends application paths to `index.html` for React Router to handle while leaving the listed static file extensions unchanged.

### Step 4: Save the settings and check the first build

Save the settings to start a build for branch `main`. Open the build log and wait for a successful status. If the build fails, check the build command, app root, and `dist` output. Add the `VITE_*` variables in Amplify before the redeployment in section 5.9.2.

## Deployment evidence

The production `main` branch is deployed on Amplify; its branch status and deployed frontend are shown in section 5.9.2. The original repository-selection and build-settings screen was not retained.

## Expected result

The `main` application branch is deployed and serving the frontend. Detailed repository/build settings were not separately captured.

## Troubleshooting

- Do not select the workshop documentation repository by mistake.
- For a monorepo, set the correct application root.
