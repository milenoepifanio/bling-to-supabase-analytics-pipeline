# Modelagem e Integração no Supabase

Arquitetura em camadas para unificação de dados Bling + WooCommerce, garantindo fonte de verdade única e consistência temporal.

## Camadas no Supabase

**Raw/Staging:** `pedidos_bling` - Dados brutos do Bling, estrutura próxima à API, sem transformações significativas. Permite reprocessamento e auditoria.

**Staging Views:** Padronização de tipos, limpeza (nulos, valores inválidos), mapeamento de status, validação com regras de negócio. Exemplo: `v_pedidos_bling_clean`.

**Integration Views:** Matching de pedidos, merge de atributos, resolução de conflitos. Unifica dados das duas fontes. Exemplo: `v_pedidos_unificados`.

**Marts:** Modelo dimensional com fato de vendas (métricas: receita, quantidade, descontos) e dimensões (Cliente, Produto, Tempo, Status, Loja). Estrutura otimizada para BI.

## Integração Bling + WooCommerce

### Estratégias de Matching

**1. Por `numeroLoja` (Preferencial):** Matching direto quando campo existe e é estável em ambas as fontes. Alta confiabilidade.

**2. Por `pedido_numero + data + valor` (Match Probabilístico):** Fallback quando não há identificador direto. Considera múltiplos fatores, pode gerar falsos positivos.

**3. Por Tabela Ponte ("Crosswalk"):** Tabela `pedidos_crosswalk` com mapeamento explícito (`bling_id`, `woocommerce_id`, `confianca`). Controle total, permite regras complexas, requer manutenção.

### Fonte de Verdade por Atributo

Cada atributo tem fonte de verdade definida: **Status logístico/entrega** (WooCommerce), **Status financeiro/faturamento** (Bling), **Valor total** (Bling), **Dados do cliente** (Bling), **Dados de pagamento** (Bling), **Dados de transporte/rastreamento** (WooCommerce). Implementado via `COALESCE` ou `CASE WHEN` em views SQL.

### Tratamento de Divergências e Atrasos

**Divergências:** Views de reconciliação comparam valores entre fontes, tabela `pedidos_divergencias` registra divergências, regras de negócio definem qual fonte prevalece, alertas para divergências críticas. Exemplo: valor total diverge > 5% → usar Bling.

**Atrasos de Sincronização:** Processamento incremental permite reprocessamento, views consideram `created_at`/`updated_at`, atualização progressiva quando pedido aparece no Bling, estado intermediário mostra pedidos "parciais" até unificação completa.
 
### Garantia de Consistência Temporal

**Timestamps:** `created_at`, `updated_at` em todas as tabelas, versionamento opcional para mudanças críticas, snapshots para análises históricas.

**Idempotência:** Controle via `bling_controle_datas`, upsert logic previne duplicação, transações SQL garantem atomicidade.

**Consistência em Views:** Views determinísticas, ordem de processamento (Bling → WooCommerce → Unificação), views materializadas atualizadas em horários definidos. Garantir que análises considerem mesmo "estado" dos dados ou documentar quando dados estão em transição.