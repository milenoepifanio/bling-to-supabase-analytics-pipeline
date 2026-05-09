# Results and Metrics

Analyses enabled by the project and recommended metrics for monitoring and optimization.

## What the Project Enables

**Consolidated ERP + E‑commerce Sales Analysis:** Unified sales view (Bling ERP + WooCommerce). Enables consolidated revenue, channel performance comparison, seasonality analysis, customer segmentation, and average order value by channel.

**Cancellation Rate by Channel:** Share of cancelled orders by origin (Bling vs WooCommerce). Surfaces operational differences, links cancellations to specific factors, and compares patterns across channels.

**Lead Time: Order (WooCommerce) → Invoicing (Bling):** Time between order creation and invoicing/billing in Bling. Helps find bottlenecks, enforce SLA, analyze trends, and alert on unusually long lead times.

**Status Divergences and Reconciliation:** Inconsistencies between Bling and WooCommerce statuses. Analyze divergence volume, patterns, resolution time, financial impact, and automatic vs manual reconciliation rates.

## Recommended Metrics

**Daily Volume of Loaded Bling Orders:** Count of orders inserted into `pedidos_bling` per date. Monitors throughput, highlights abnormal days, and validates completeness.

**Error Rate per Date:** Share of orders that failed during processing. Formula: `(pedidos_com_erro / pedidos_totais) * 100`. Surfaces problematic dates and validates pipeline quality.

**Execution Time per Date:** Total processing duration (start to end). Tracks performance, spots degradation, and estimates historical backfill time.

**Bling ↔ WooCommerce Match Rate:** Share of Bling orders successfully unified with WooCommerce. Validates integration quality and flags orphan orders. Rule of thumb: above ~90% is healthy; below ~70% warrants investigation; a declining trend may indicate sync or upstream data drift.

## Recommended Dashboard

Consolidated view: daily volume (line), error rate (bars), execution time (line), match rate (KPI), alerts when metrics stray from expectations. Refresh daily after each date finishes processing.
