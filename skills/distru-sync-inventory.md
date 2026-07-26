---
name: Sync Distru inventory and products
description: Pull the current product catalog and on-hand inventory from Distru and reconcile it against an external system.
api: openapi/distru-openapi.yml
operations: [listProducts, getProduct, listInventory, listBatches, listPackages]
---

# Sync Distru inventory and products

Use this skill to read Distru's catalog and stock levels for reporting or to keep an external system in sync.

## Auth
Send `Authorization: Bearer <API_KEY>` on every request. Keys are created in the Distru app under Settings > Integrations > Distru API and expire one year after issue. Base URL is `https://app.distru.com/public/v1`.

## Steps
1. `listProducts` — page through the catalog using `page[number]` and `page[size]` (max 500). Follow the `next_page` URL until it is null.
2. For any product needing detail, call `getProduct` with its UUID.
3. `listInventory` — page through on-hand quantities by product/location/package.
4. `listBatches` and `listPackages` — resolve batch- and package-level stock and lineage where you need traceability.
5. Reconcile against your external records; treat UUIDs as the stable join key.

## Conventions and errors
- Paginate with `page[number]`/`page[size]`; never assume a single page.
- Keep requests under ~2/second (no enforced limit, but recommended).
- Handle `401` (bad/expired key), `404` (unknown id), `422` (validation), `500` (retry with backoff). See errors/distru-problem-types.yml.
- No webhooks: poll on a schedule for changes.
