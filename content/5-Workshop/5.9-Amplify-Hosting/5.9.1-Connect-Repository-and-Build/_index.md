---
title: "Connect the Frontend Repository and Configure Build"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.9.1 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Connect Amplify to the repository containing UniCloud DMS, not the workshop documentation repository.

## Implementation steps

1. Connect the Git provider and select the frontend repository with its package.json and intended branch.
2. Configure the build to use the repository's lockfile; for npm this is normally `npm ci` followed by `npm run build`.
3. Set the Vite artifact directory to `dist` and configure SPA rewrites for React Router routes.
4. Review build settings before saving.

## Screenshot placeholder

**[Screenshot placeholder 5.9.1 — Amplify repository/branch selection and frontend build settings.]**

## Expected result

Amplify is connected to the correct application source and recognizes the build output.

## Troubleshooting

- Do not select the workshop documentation repository by mistake.
- For a monorepo, set the correct application root.
