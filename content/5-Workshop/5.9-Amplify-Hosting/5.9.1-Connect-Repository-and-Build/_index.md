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

1. Connect the Git provider and select the frontend repository with its package.json and intended branch.
2. Configure the build to use the repository's lockfile; for npm this is normally `npm ci` followed by `npm run build`.
3. Set the Vite artifact directory to `dist` and configure SPA rewrites for React Router routes.
4. Review build settings before saving.

## Deployment evidence

The production `main` branch is deployed on Amplify; its branch status and deployed frontend are shown in section 5.9.2. The original repository-selection and build-settings screen was not retained.

## Expected result

The `main` application branch is deployed and serving the frontend. Detailed repository/build settings were not separately captured.

## Troubleshooting

- Do not select the workshop documentation repository by mistake.
- For a monorepo, set the correct application root.
