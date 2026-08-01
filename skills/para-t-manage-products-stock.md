---
name: Manage products, warehouses and stock levels
description: Maintain the Paraşüt product catalog, warehouses, and adjust/track inventory levels.
api: openapi/para-t-openapi-original.yml
operations: [createProduct, listProducts, createWarehouse, createStockUpdate, listInventoryLevels]
---

# Manage products, warehouses and stock levels

Keep the Paraşüt inventory catalog and stock accurate. Company-scoped as usual:
`https://api.parasut.com/v4/{company_id}`.

## Rules
- Auth + JSON:API + 10 req / 10 s rate limit.
- `inventory_tracking` must be enabled on a product before its stock can move.

## Steps
1. **Catalog the product** — `createProduct` (`data.type: "products"`) with name, unit, prices and `inventory_tracking: true` where stock matters. Use `listProducts` (with `filter[name]`) to avoid duplicates.
2. **Set up a warehouse** — `createWarehouse` if the stock lives in a new location; keep the warehouse `id`.
3. **Adjust stock** — `createStockUpdate` to set/correct the on-hand quantity for a product in a warehouse.
4. **Read current levels** — `listInventoryLevels` (filter by product/warehouse) to report available quantities.

## Notes
- Actual movements from invoices/bills appear via `listStockMovements`; `createStockUpdate` is for manual corrections/opening balances.
