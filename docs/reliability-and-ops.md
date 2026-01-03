# Confiabilidade e Operações

Estratégias implementadas e recomendadas para garantir robustez, idempotência e auditabilidade do pipeline.

## O que Já Existe

**Retry para Erros 429:** Retry automático com até 3 tentativas (espera progressiva: 15s, 30s, 45s). Resiliência a limites temporários da API.

**Controle por Datas Pendentes:** Tabela `bling_controle_datas` controla datas processadas (status ✅). Sempre processa a data mais antiga pendente, permitindo retomada após falhas.

**Tratamento de Nulos e Tipos:** Funções `safe_*` (`safe()`, `safe_date()`, `safe_int()`, `safe_num()`) garantem qualidade de dados na ingestão, evitando erros de tipo e padronizando valores nulos.

## Recomendações (Aplicadas no Supabase)

**Constraints e Unique Keys:** Unique constraints em `pedido_id` e `pedido_numero`, NOT NULL em campos críticos, check constraints para validação. Previne duplicação e garante integridade.

**Idempotência via Upsert:** `INSERT ... ON CONFLICT (pedido_id) DO UPDATE` permite reexecutar pipeline sem duplicar dados, atualizando registros existentes quando necessário. Aplicado no Supabase para garantir consistência no reprocessamento.

**Tabela `etl_runs` para Auditoria:** Estrutura com `data_processada`, `inicio_execucao`, `fim_execucao`, `status`, `pedidos_processados`, `pedidos_com_erro`, `duracao_segundos`. Permite rastreabilidade, identificação de problemas e análise de performance.

## Estratégia de Confiabilidade

**Camadas de Proteção:** Pipeline (retry, tratamento de tipos, controle de datas) → Supabase Constraints (prevenção de duplicação) → Supabase Upsert (idempotência) → Auditoria (rastreabilidade).

**Recuperação:** Em caso de falha, data permanece pendente. Próxima execução identifica data pendente, upsert evita duplicação de pedidos já processados, processamento continua de onde parou. Para reprocessar data já processada: resetar status em `bling_controle_datas`, reexecutar pipeline, upsert atualiza/insere dados, constraints garantem integridade.
