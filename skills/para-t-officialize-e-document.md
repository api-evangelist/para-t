---
name: Officialize a sales invoice as an e-Arşiv / e-Fatura document
description: Turn an existing Paraşüt sales invoice into an official Turkish e-document, handling the asynchronous TrackableJob and PDF retrieval.
api: openapi/para-t-openapi-original.yml
operations: [listEInvoiceInboxes, createEArchive, createEInvoice, showTrackableJob, showEArchivePdf]
---

# Officialize a sales invoice as an e-Arşiv / e-Fatura document

Given a sales invoice `id`, make it a legal Turkish e-document. e-document creation
is **asynchronous** — you must poll a TrackableJob.

## Rules
- Auth + JSON:API + rate limit as in the invoicing skill.
- The create call does **not** return the finished document; it returns a trackable job `id`, valid for **15 minutes**.
- Generated PDF URLs are valid for **1 hour** — download and forward them yourself; never share the raw link.

## Steps
1. **Decide e-Fatura vs e-Arşiv** — call `listEInvoiceInboxes` with the customer's tax number (VKN). If an inbox is returned, the customer is an e-Fatura user → use `createEInvoice`. Otherwise → use `createEArchive`. Export invoices are always e-Fatura.
2. **Create the e-document** — `createEArchive` (or `createEInvoice`) referencing the sales invoice `id`. Keep the returned trackable job `id`.
3. **Poll the job** — call `showTrackableJob` with that id at intervals until status is done (or an error). Do this within the 15-minute window.
4. **Fetch document details** — `showSalesInvoice` with `?include=active_e_document` to read the resulting e-document `id`.
5. **Retrieve the PDF** — `showEArchivePdf` with the e-document id. It returns `204` (empty) while the PDF is still generating; retry until you get a valid response, then download the 1-hour URL.

## Notes
- e-SMM (self-employment receipt) follows the same shape via `createESmm` / `showESmmPdf`.
