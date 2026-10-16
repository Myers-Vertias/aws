---
title: "Test Favorites, Tags, Trash, and VI/EN"
date: 2026-10-16
weight: 3
chapter: false
pre: " <b> 5.10.3 </b> "
---

{{% notice info %}}
This is a prepared guide. Replace the placeholder with evidence only after the corresponding step has been performed and verified.
{{% /notice %}}

## Objective

Verify per-user features, stable stored codes, soft deletion, and language persistence.

## Implementation steps

1. Test favorite/unfavorite with two users and confirm the state is per-user.
2. Set categories and tags, search/filter, and verify stored values do not change when the UI language changes.
3. Move an owned document to Trash, restore it, and test permanent delete only with disposable data.
4. Switch Vietnamese/English, refresh, and navigate through all pages; record any mixed-language labels.

## Screenshot placeholder

**[Screenshot placeholder 5.10.3 — Favorites/Trash test and the same UI page in Vietnamese and English.]**

## Expected result

Feature behavior, data invariants, and language persistence have actual test records.

## Troubleshooting

- Never use valuable files for permanent-delete testing.
- Stored category/status values must not become translated display strings.
