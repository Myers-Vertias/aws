---
title: "Test Sharing and Authorization with Two Users"
date: 2026-10-16
weight: 2
chapter: false
pre: " <b> 5.10.2 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Verify Owner/Viewer rules, separation of My Documents and Shared With Me, and share revocation.

## Implementation steps

1. User A uploads a sample document and shares it with User B.
2. As User B, verify the document appears under Shared With Me, not My Documents; verify view/download works but edit/share/delete is denied.
3. User A revokes access. Confirm User B cannot request new metadata or download URLs.
4. Test no-token and third-user requests directly against the API. Record the actual responses.

## Screenshot placeholder

**[Screenshot placeholder 5.10.2 — User B shared list and denied actions after share revocation.]**

## Expected result

Only owners and active viewers can access a document according to the rules.

## Troubleshooting

- Previously issued presigned URLs work until expiry; use a short lifetime.
- Ensure authorization is enforced by Lambda, not just by React.
