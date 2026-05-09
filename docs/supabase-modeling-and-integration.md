# Modeling and Integration in Supabase

Layered architecture for Bling + WooCommerce consolidation, aiming for a single source of truth and temporal consistency.

## Layers in Supabase

**Raw/Staging:** `pedidos_bling` — Raw Bling data, structure close to the API, minimal transformation. Enables reprocessing and auditing.

**Staging Views:** Type standardization, cleanup (nulls, invalid values), status mapping, business-rule validation. Example: `v_pedidos_bling_clean`.

**Integration Views:** Order matching, attribute merge, conflict resolution. Unifies data from both sources. Example: `v_pedidos_unificados`.

**Marts:** Dimensional model with a sales fact (metrics: revenue, quantity, discounts) and dimensions (Customer, Product, Time, Status, Store). Structured for BI consumption.

## Bling + WooCommerce Integration

### Matching Strategies

**1. By `numeroLoja` (Preferred):** Direct match when the field exists and is stable in both sources. High confidence.

**2. By `pedido_numero + date + amount` (Probabilistic match):** Fallback when there is no direct identifier. Uses multiple signals; false positives are possible.

**3. Via bridge table (“crosswalk”):** `pedidos_crosswalk` with explicit mapping (`bling_id`, `woocommerce_id`, `confianca`). Full control and complex rules, but ongoing maintenance required.

### Source of Truth by Attribute

Each attribute has a defined source of truth: **Logistics/delivery status** (WooCommerce), **Financial/invoicing status** (Bling), **Total amount** (Bling), **Customer data** (Bling), **Payment data** (Bling), **Shipping/tracking data** (WooCommerce). Implemented via `COALESCE` or `CASE WHEN` in SQL views.

### Divergences and Lag

**Divergences:** Reconciliation views compare values across sources; `pedidos_divergencias` records mismatches; business rules decide which source wins; alerts for critical gaps. Example: if total amounts differ by more than about 5%, prefer Bling.

**Sync lag:** Incremental processing allows reprocessing; views use `created_at` / `updated_at`; gradual refresh when an order lands in Bling; intermediate states can show “partial” orders until full unification.

### Temporal Consistency

**Timestamps:** `created_at`, `updated_at` on tables, optional versioning for critical changes, snapshots for historical analysis.

**Idempotency:** Controlled via `bling_controle_datas`, upsert logic prevents duplication, SQL transactions preserve atomicity.

**View consistency:** Deterministic views, processing order (Bling → WooCommerce → Unified), materialized views refreshed on a schedule. Analyses should use a consistent snapshot of state—or document when data is mid-transition.
