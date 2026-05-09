# Data Sources

## Bling API v3

**Endpoints used:**
- `GET /Api/v3/pedidos/vendas` — Lists sales by date (`dataInicial` / `dataFinal`)
- `GET /Api/v3/pedidos/vendas/{id}` — Full details for a specific order

**Authentication:** Bearer token (from the `integracoes` table in Supabase)  
**Rate limiting:** 9 seconds between requests, automatic retry on 429 errors  
**Data collected:** Orders with complete information (customer, payment, shipping, line items, etc.)

## Real-World Challenges

### Different IDs between Bling and WooCommerce

Bling and WooCommerce use different identification schemes for the same orders. Consolidation happens in Supabase through SQL logic that finds matching orders (likely via order number, date, customer, or other shared fields).

### Different Status Rules

Each platform has its own order-status model:
- **Bling:** ERP-specific statuses (e.g., open, invoiced, cancelled)
- **WooCommerce:** E‑commerce statuses (e.g., pending, processing, completed, cancelled)

Status normalization and mapping is done in the Supabase SQL layer during consolidation.

### Possible Sync Lag

Orders may appear in WooCommerce before they are invoiced in the ERP (Bling), creating temporary inconsistencies. The consolidation logic in Supabase must handle:
- Orders that exist on one platform but not the other (temporarily)
- Need for reprocessing or refresh when the order appears on the second platform
- Detection of duplicate or related orders

**Approach:** Incremental processing and robust consolidation logic in the SQL layer, with the option to reprocess when new data arrives.
