---
title: "Test Sharing and Download Visibility with Two Users"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.10.2 </b> "
---

{{% notice info %}}
This report records only steps performed and verified. Add evidence only after completing the corresponding step.
{{% /notice %}}

## Objective

Verify that an owner can share a document with another account, and that the recipient can see the shared document and initiate its download.

## Steps performed

1. Sign in as the document owner and upload a document.
2. Share the document with the recipient account's email address.
3. Sign in as the recipient account.
4. Open **Shared With Me**, confirm the document appears, and select Download.

## Verified result

The recipient account displayed the document under **Shared With Me**. After Download was selected, the browser's download history listed `Konoka.png` with status **Done**. The screenshots record the shared-document view and the browser download result.

On November 18, 2026, while signed in as a different account, a read-only authenticated `GET /documents/{documentId}` request for an owner document that was not shared with that account returned HTTP `404` with `Document not found`. The request did not modify data. This confirms that the tested account could not retrieve the unshared document; the API uses not-found for documents outside the caller's access.

## Evidence

![Screenshot 5.10.2: Recipient sees shared documents on the production frontend](/images/5-Workshop/5.10-End-to-End-Testing/5.10.2-shared-with-me-production.png)

![Screenshot 5.10.2: Recipient opened the shared document and browser download history shows the completed download in the local run](/images/5-Workshop/5.10-End-to-End-Testing/5.10.2-recipient-document-download-local.png)

![Screenshot 5.10.2: Authenticated read-only request for an unshared document returns HTTP 404](/images/5-Workshop/5.10-End-to-End-Testing/5.10.2-unshared-document-access-denied-404.png)
