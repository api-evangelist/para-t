---
name: Create a contact and issue a sales invoice
description: Register a customer (contact), ensure the products exist, then create and collect a sales invoice in Paraşüt.
api: openapi/para-t-openapi-original.yml
operations: [createContact, createProduct, createSalesInvoice, showSalesInvoice, paySalesInvoice]
---

# Create a contact and issue a sales invoice

Use the Paraşüt API V4 to bill a customer end to end. All calls are scoped to a
company: `https://api.parasut.com/v4/{company_id}`.

## Rules
- Auth: `Authorization: Bearer <access_token>` (OAuth2; token valid 2 hours, refresh with the rotating `refresh_token`).
- Send `Content-Type: application/vnd.api+json`; request and response bodies follow JSON:API (`data.type`, `data.attributes`, `data.relationships`).
- Rate limit: 10 requests / 10 seconds.
- Errors come back as a JSON:API `errors[]` array (`title`, `detail`); `422` means attribute validation failed.

## Steps
1. **Create the customer** — `createContact` with `data.type: "contacts"` and the customer's name, tax info and address in `attributes`. Keep the returned contact `id`.
2. **Ensure line items exist** — `createProduct` for any product/service not already in the catalog; keep each product `id`. (Use `listProducts` to look up existing ones.)
3. **Create the sales invoice** — `createSalesInvoice` with `data.type: "sales_invoices"`; reference the contact via `data.relationships.contact` and add `details` line items referencing product ids, quantities and unit prices.
4. **Confirm** — `showSalesInvoice` with `?include=details,contact` to verify totals and the persisted line items.
5. **Collect payment** — `paySalesInvoice` with the amount, date and the account id the payment lands in.

## Notes
- This creates a *draft/internal* invoice. To make it a legal Turkish e-document, follow the `para-t-officialize-e-document` skill afterward.
