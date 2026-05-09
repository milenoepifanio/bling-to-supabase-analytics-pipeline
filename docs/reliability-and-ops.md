# Reliability and Operations

Implemented and recommended strategies for pipeline robustness, idempotency, and auditability.

## What Already Exists

**Retry on 429 errors:** Automatic retry with up to 3 attempts (progressive backoff: 15s, 30s, 45s). Resilience to temporary API limits.

**Pending-date control:** The `bling_controle_datas` table tracks processed dates (✅ status). Always processes the oldest pending date, enabling resume after failures.

**Null and type handling:** `safe_*` functions (`safe()`, `safe_date()`, `safe_int()`, `safe_num()`) safeguard ingestion quality, avoiding type errors and standardizing nulls.

## Recommendations (Applied in Supabase)

**Constraints and unique keys:** Unique constraints on `pedido_id` and `pedido_numero`, NOT NULL on critical fields, check constraints for validation. Prevents duplication and upholds integrity.

**Idempotency via upsert:** `INSERT ... ON CONFLICT (pedido_id) DO UPDATE` lets you rerun the pipeline without duplicating data, updating existing rows when needed. Applied in Supabase for consistent reprocessing.

**`etl_runs` table for auditing:** Structure with `data_processada`, `inicio_execucao`, `fim_execucao`, `status`, `pedidos_processados`, `pedidos_com_erro`, `duracao_segundos`. Enables traceability, issue detection, and performance analysis.

## Reliability Strategy

**Defense in depth:** Pipeline (retry, type handling, date control) → Supabase constraints (duplicate prevention) → Supabase upsert (idempotency) → Audit trail (traceability).

**Recovery:** On failure, the date stays pending. The next run picks the pending date, upsert avoids duplicating orders already processed, and processing resumes where it stopped. To reprocess an already-processed date: reset status in `bling_controle_datas`, rerun the pipeline—upsert updates/inserts data and constraints preserve integrity.
