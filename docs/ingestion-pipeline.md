# Ingestion Pipeline

Bling order ingestion pipeline. Incremental day-by-day processing with date control.

## Pipeline Flow

### 1. Credential Read

Load Bling `token` and `refresh_token` from the `integracoes` table in Supabase (`id = 1`).

### 2. Next Date Selection

Query `bling_controle_datas` for the next pending date (status not ✅). Always processes the **oldest pending date**.

### 3. Daily Order List

Call the Bling API (`GET /Api/v3/pedidos/vendas`) with `dataInicial` and `dataFinal` equal to the selected date. Returns IDs and numbers for orders.

### 4. Per-Order Detail Fetch

For each order found:
- Call the Bling API (`GET /Api/v3/pedidos/vendas/{id}`)
- **Automatic retry** on 429 (rate limit):
  - Up to 3 attempts
  - Progressive wait: 15s, 30s, 45s
- **9-second** pause between requests to honor rate limits

### 5. Field Standardization

Apply `safe_*` helpers for normalization:
- `safe()` — Handles undefined or empty values
- `safe_date()` — Validates and handles invalid dates
- `safe_int()` — Integer conversion with error handling
- `safe_num()` — Decimal conversion with error handling

### 6. Save to Supabase

Insert normalized rows into `pedidos_bling`.

### 7. Mark Date as Processed

Update the date status in `bling_controle_datas` to ✅ after all orders for the day are processed.

## Explicit Limitations

### Synchronous Processing

Orders are handled **sequentially** (one at a time). No parallel processing—respects rate limits but caps throughput.

### Dependency on Bling Rate Limits

The pipeline is bound by Bling API rate limiting:
- **9 seconds** mandatory wait between detail requests
- Direct impact on total runtime (e.g., 100 orders ≈ ~15 minutes minimum)

### Manual Restart (Backfill)

To process multiple dates (historical backfill):
- You must **restart execution** after each processed date
- Run the pipeline again for the next date
- Manual process, not automated across consecutive dates in one shot

**Note:** Orchestrator-based automation partly mitigates this, but each run still processes one date at a time.
