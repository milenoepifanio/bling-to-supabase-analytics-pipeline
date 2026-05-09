# Bling Orders Import — Overview

Ingestion pipeline for historical Bling orders into Supabase with incremental processing. Data is consolidated in Supabase alongside WooCommerce, yielding dimension and fact tables ready for BI and operational metrics (revenue, cancellation, SLA).

## Problem

Need for a single, trustworthy base for orders and analytics. Manual historical processing is heavy and error-prone. Lack of visibility into which periods have been processed.

## Solution

Python pipeline for incremental ingestion via the Bling API. Day-by-day processing with date control in Supabase. **Bling + WooCommerce integration happens in the Supabase SQL layer** (prior workflow via N8N by another analyst). Structured analytics layer with dimensions and facts.

## Main Features

- Incremental processing (date by date) controlled via `bling_controle_datas`
- Rate limiting (9s between requests) and automatic retry on 429
- Robust data handling (type validation, dates, nulls)
- Automated execution (manual or scheduled)
- Analytics-ready dimension and fact tables for BI

## Stack / Technologies

**Backend:** Python 3.10, Supabase Python client, pandas, requests

**Database:** PostgreSQL via Supabase. Tables: `integracoes`, `bling_controle_datas`, `pedidos_bling`. SQL views/functions and CRON jobs for Bling + WooCommerce consolidation (creating `id_potencia`, removing duplicates).

**Infrastructure:** Orchestration and automation (manual or scheduled execution)

**API:** Bling API v3 with Bearer-token authentication and rate limiting

## What Is Implemented

Supabase connectivity, credential fetch, incremental date processing (always the oldest pending date), listing sales by date, full detail fetch, data normalization (`safe_*` helpers), inserts into `pedidos_bling`, date status tracking, rate limiting (9s), automatic retry on 429, automated execution, error handling.

## Architecture

**Separation of concerns:**
- **Pipeline:** Ingest data from Bling
- **Supabase (SQL):** Transformations, Bling + WooCommerce consolidation, `id_potencia` creation, duplicate removal

**Flow:** Connect to Supabase → Fetch credentials (`integracoes`) → Identify next pending date (`bling_controle_datas`) → List sales (Bling API) → Process each order (details, normalization, insert) → Mark date as processed (✅)

**Fields processed:** ID/number, dates (created, shipped, expected), totals/discounts, customer, status, store, payment, shipping, address, seller, intermediary, fees, notes.
