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

Billing evidence was captured on November 18, 2026. Cost Explorer was grouped by service for November 1–18 and displayed a total of `-$0.00` with no visible cost bars. The November Bills page displayed an estimated total of `USD 0.00` with status `Pending`. These are the values displayed at the time of review, not a finalized invoice. Account identifiers are redacted in the screenshots below.

![Cost Explorer summary for November 1–18, 2026; displayed total is -$0.00](/images/5-Workshop/5.12-Cleanup/5.12.1-cost-explorer-summary-nov-2026.png)

![Cost Explorer service-grouped graph for November 1–18, 2026; no cost bars are visible](/images/5-Workshop/5.12-Cleanup/5.12.1-cost-explorer-graph-nov-2026.png)

![AWS Bills page for November 2026; estimated total is USD 0.00 and status is Pending](/images/5-Workshop/5.12-Cleanup/5.12.1-bills-nov-2026.png)

## Final verification matrix

| Area | Result | Evidence / limitation |
|---|---|---|
| SAM validation/build and deployed stack | Verified | Sections 5.1.1, 5.2.2, and 5.9.2 |
| Cognito sign-up, confirmation, and sign-in | Verified | Sections 5.3.1, 5.3.2, and 5.8.2 |
| API authorization | Verified | Authenticated list `200`, missing-token request `401`, and unshared cross-account detail `404`; section 5.7.2 |
| Upload, download, metadata, sharing, Favorites, Trash/restore | Verified | Sections 5.8.2, 5.10.1–5.10.3 |
| VI/EN selection, category filter, tag search | Verified | Section 5.10.3; UI checks were performed on the local frontend where stated |
| CloudWatch runtime logs | Partially verified | Lambda `START`/`END`/`REPORT` events observed; no route/status correlation; section 5.11.1 |
| Oversized-file rejection | Verified (frontend and deployed API) | The local UI rejected the oversized file; direct authenticated `POST /documents/upload-url` with 20 MiB + 1 byte returned HTTP `400` before issuing an S3 URL; section 5.10.1 |
| Unshared cross-account document read | Verified | Authenticated read-only `GET /documents/{documentId}` returned HTTP `404` for an owner's unshared document; section 5.10.2 |
| Post-deployment billing review | Partially completed | November 1–18 Cost Explorer showed `-$0.00`; November Bills showed `USD 0.00`, status `Pending`. This is provisional, not a final invoice; screenshots above |
| Resource cleanup | Not done | AWS resources are retained so the application can continue running; see section 5.12.2 |

No single composite dashboard was captured. The individual screenshots and linked evidence above are the record for this run.

## Expected result

The workshop report records the implemented architecture and observed application outcomes.

## Troubleshooting

- Do not create illustrative fake screenshots or mark expected results as actual results.
- Keep report images and source code after cloud resources are removed.
