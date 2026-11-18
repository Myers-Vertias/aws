---
title: "Connect the Frontend Repository and Configure Build"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.9.1 </b> "
---

{{% notice info %}}
This page records the deployed implementation and observed results. Any unverified behavior is identified explicitly.
{{% /notice %}}

## Objective

Connect Amplify to the repository containing UniCloud DMS, not the workshop documentation repository.

## Implementation steps

1. In AWS Amplify, choose Create new app → Host web app, connect GitHub, and authorize repository selection. Select the `serverless-document-management` application repository, not the workshop documentation repository.
2. Select the branch to publish (the project uses `main`); confirm the app root contains `package.json` and `package-lock.json`.
3. Set build command to `npm ci && npm run build`, artifact directory to `dist`, and add the SPA rewrite so React Router refreshes return `index.html`.
4. Save settings and let Amplify run the initial build. Add `VITE_*` environment variables in Amplify before redeploying, as described in section 5.9.2.

## Deployment evidence

The production `main` branch is deployed on Amplify; its branch status and deployed frontend are shown in section 5.9.2. The original repository-selection and build-settings screen was not retained.

## Expected result

The `main` application branch is deployed and serving the frontend. Detailed repository/build settings were not separately captured.

## Troubleshooting

- Do not select the workshop documentation repository by mistake.
- For a monorepo, set the correct application root.
