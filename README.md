# Bling Orders Import → Supabase

Incremental ingestion pipeline for historical Bling orders to Supabase, creating a unified analytical base with WooCommerce for business intelligence.

## 📋 About the Project

This project performs the import and processing of historical orders from **Bling** (ERP) to **Supabase**, processing data day by day in an automated and controlled manner. Data is unified in Supabase with **WooCommerce** (e-commerce), creating dimension and fact tables ready for analysis and operational metrics (revenue, cancellation, SLA).

> **⚠️ Important:** The Bling + WooCommerce integration occurs in the Supabase SQL layer. This happens because the previous flow was implemented by another analyst via N8N, and the integration logic is already consolidated in the Supabase SQL layer.

## 🎯 Objective

Create a single and reliable base for orders and sales/operational analysis, solving the problem of manual processing of historical data and lack of control over processed periods.

## 🏗️ Architecture

### Processing Flow

1. Connect to Supabase
2. Fetch Bling credentials (`integracoes`)
3. Identify next pending date (`bling_controle_datas`)
4. List sales for the date (Bling API)
5. Process each order (details, normalization, insertion)
6. Mark date as processed (✅)

### Supabase Layers

- **Raw/Staging:** `pedidos_bling` (pipeline output)
- **Staging Views:** Standardization, cleaning, types, status
- **Integration Views:** Bling ↔ WooCommerce join
- **Marts:** Facts and dimensions for analytical consumption

## 🛠️ Stack

- **Backend:** Python 3.10, Supabase Python client, pandas, requests
- **Database:** PostgreSQL via Supabase
- **API:** Bling API v3
- **Infrastructure:** Orchestration and automation (manual or scheduled execution)

## ✨ Features

- ✅ Incremental processing (day by day) with control via `bling_controle_datas`
- ✅ Rate limiting (9s between requests, automatic retry for 429)
- ✅ Robust data handling (type, date, null validation)
- ✅ Automated execution (manual or scheduled)
- ✅ Analytical base with dimension and fact tables ready for BI

## 📊 Results

The project enables:

- Consolidated sales analysis ERP + e-commerce
- Cancellation rate by channel
- Lead time: order (WooCommerce) → billing (Bling)
- Status divergences and reconciliation

## 🔒 Reliability

- Automatic retry for 429 errors (rate limit)
- Pending dates control for incremental processing
- Null and type handling (`safe_*` functions)
- Idempotency via upsert in Supabase
- Constraints and unique keys to ensure integrity

## 📚 Documentation

Complete documentation available in `docs/`:

- **[Overview](docs/overview.md)** - Project overview, stack, architecture and flow
- **[Data Sources](docs/data-sources.md)** - Bling API v3, WooCommerce and integration challenges
- **[Ingestion Pipeline](docs/ingestion-pipeline.md)** - Detailed pipeline flow and limitations
- **[Modeling and Integration](docs/supabase-modeling-and-integration.md)** - Layered architecture, matching strategies and unification
- **[Reliability and Operations](docs/reliability-and-ops.md)** - Retry, idempotency, constraints and auditing
- **[Results and Measurement](docs/results-and-measurement.md)** - Enabled analyses and recommended metrics

## 📄 License

This project was developed as a data engineering case study.