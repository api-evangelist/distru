---
name: Create a Distru order and invoice it
description: Create or update a sales order for a company, then generate and record payment against its invoice.
api: openapi/distru-openapi.yml
operations: [listCompanies, upsertOrder, getOrder, createInvoice, createInvoicePayment]
---

# Create a Distru order and invoice it

Use this skill to place a wholesale order for a customer company and take it through invoicing and payment.

## Auth
Send `Authorization: Bearer <API_KEY>` on every request. Base URL `https://app.distru.com/public/v1`.

## Steps
1. `listCompanies` — find the customer company UUID (page with `page[number]`/`page[size]`).
2. `upsertOrder` — create or update the sales order (upsert semantics: sending the same logical order updates it in place rather than duplicating). Reference the company and order items (products + quantities).
3. `getOrder` — confirm the persisted order and its totals.
4. `createInvoice` — generate the invoice for the order.
5. `createInvoicePayment` (`POST /invoices/{id}/payments`) — record a payment against the invoice, referencing a payment method.

## Conventions and errors
- There is no idempotency-key header; rely on upsert semantics and read-back with `getOrder` to avoid duplicates.
- Validate inputs before writing; `422` indicates a validation failure with a message naming the field.
- `401` means a missing/expired key; `404` means an unknown company/order/invoice id.
- See conventions/distru-conventions.yml and errors/distru-problem-types.yml.
