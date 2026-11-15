---
title: "Capture Final Evidence"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 5.12.1 </b> "
---

{{% notice info %}}
This page records the deployed implementation and observed results. Any unverified behavior is identified explicitly.
{{% /notice %}}

## Objective

Collect genuine screenshots and test records before deleting resources.

## Evidence collected

The report reuses screenshots captured during the work instead of repeating near-identical images. Configuration evidence is in sections 5.2–5.9; the production application and real-account workflows are in sections 5.8 and 5.10; CloudWatch observations are in section 5.11. Sensitive account identifiers, role ARNs, and document details are redacted in report copies where applicable.

## Final verification matrix

| Area | Result | Evidence / limitation |
|---|---|---|
| SAM validation/build and deployed stack | Verified | Sections 5.1.1, 5.2.2, and 5.9.2 |
| Cognito sign-up, confirmation, and sign-in | Verified | Sections 5.3.1, 5.3.2, and 5.8.2 |
| API authorization | Verified | Authenticated list `200`, missing-token request `401`, and unshared cross-account detail `404`; section 5.7.2 |
| Upload, download, metadata, sharing, Favorites, Trash/restore | Verified | Sections 5.8.2, 5.10.1–5.10.3 |
| VI/EN selection, category filter, tag search | Verified | Section 5.10.3; UI checks were performed on the local frontend where stated |
| CloudWatch runtime logs | Partially verified | Lambda `START`/`END`/`REPORT` events observed; no route/status correlation; section 5.11.1 |
| Oversized upload, expired-token behavior, share revocation/duplicate/self-share, Viewer write denial | Not verified | No result is claimed; see section 5.6.3 and related test notes |
| Permanent deletion and S3 lifecycle expiration | Not verified | Destructive/expiry behavior was not exercised; no result is claimed |
| Post-deployment billing review and resource cleanup | Not done | Resources are retained; see section 5.12.2 |

No single final screenshot or composite dashboard was captured. The linked evidence above is the final record for this run.

## Expected result

The workshop report distinguishes completed implementation, observed results, partial observations, and unverified work.

## Troubleshooting

- Do not create illustrative fake screenshots or mark expected results as actual results.
- Keep report images and source code after cloud resources are removed.
