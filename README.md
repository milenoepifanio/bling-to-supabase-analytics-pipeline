# Bling Orders Import → Supabase

Incremental ingestion pipeline for historical orders from Bling into Supabase, building a unified analytics layer with WooCommerce for business intelligence.

![Python](https://img.shields.io/badge/Python-Professional-blue)
![Data Engineering](https://img.shields.io/badge/Data_Engineering-Ingestion-green)
![Supabase](https://img.shields.io/badge/Supabase-PostgreSQL-3ECF8E)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Analytics-316192)
![REST API](https://img.shields.io/badge/API-Bling_V3-orange)
![Analytics](https://img.shields.io/badge/Analytics-BI_Ready-purple)

## 📋 About the Project

This project imports and processes historical **Bling** (ERP) orders into **Supabase**, processing data day by day in an automated, controlled way. Data is consolidated in Supabase alongside **WooCommerce** (e‑commerce), producing dimension and fact tables ready for analysis and operational metrics (revenue, cancellation, SLA).

> **⚠️ Important:** Bling + WooCommerce integration happens at the Supabase SQL layer. This is because the previous workflow was implemented by another analyst via N8N, and the integration logic is already consolidated in Supabase.

## 🎯 Goal

Provide a single, trustworthy source for orders and sales/operations analytics, addressing manual historical processing and lack of visibility into which periods have been processed.

## 🏗️ Architecture

### Processing Flow

1. Connect to Supabase
2. Fetch Bling credentials (`integracoes`)
3. Identify the next pending date (`bling_controle_datas`)
4. List sales for that date (Bling API)
5. Process each order (details, normalization, insertion)
6. Mark the date as processed (✅)

### Layers in Supabase

- **Raw/Staging:** `pedidos_bling` (pipeline output)
- **Staging Views:** Standardization, cleanup, types, statuses
- **Integration Views:** Bling ↔ WooCommerce joins
- **Marts:** Facts and dimensions for analytics consumption

## 🛠️ Stack

- **Backend:** Python 3.10, Supabase Python client, pandas, requests
- **Database:** PostgreSQL via Supabase
- **API:** Bling API v3
- **Infrastructure:** Orchestration and automation (manual or scheduled execution)

## ✨ Features

- ✅ Incremental processing (day by day) controlled via `bling_controle_datas`
- ✅ Rate limiting (9s between requests, automatic retry on 429)
- ✅ Robust data handling (types, dates, nulls)
- ✅ Automated execution (manual or scheduled)
- ✅ Analytics-ready dimension and fact tables for BI

## 📊 Outcomes

The project enables:

- Consolidated sales analysis (ERP + e‑commerce)
- Cancellation rate by channel
- Lead time: order (WooCommerce) → invoicing/billing (Bling)
- Status discrepancies and reconciliation

## 🔒 Reliability

- Automatic retry on 429 (rate limit)
- Pending-date control for incremental processing
- Null and type handling (`safe_*`)
- Idempotency via upsert to Supabase
- Constraints and unique keys for integrity

## 📚 Documentation

Full documentation lives under `docs/`:

- **[Overview](docs/overview.md)** — Project overview, stack, architecture, and flow
- **[Data Sources](docs/data-sources.md)** — Bling API v3, WooCommerce, and integration challenges
- **[Ingestion Pipeline](docs/ingestion-pipeline.md)** — Detailed pipeline flow and limitations
- **[Modeling & Integration](docs/supabase-modeling-and-integration.md)** — Layered architecture, matching strategies, and consolidation
- **[Reliability & Operations](docs/reliability-and-ops.md)** — Retry, idempotency, constraints, and audit
- **[Results & Metrics](docs/results-and-measurement.md)** — Supported analyses and recommended metrics

## 📄 License

This project was developed as a data engineering case study.
