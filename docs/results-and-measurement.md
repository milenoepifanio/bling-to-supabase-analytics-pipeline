# Resultados e Métricas

Análises habilitadas pelo projeto e métricas recomendadas para monitoramento e otimização.

## O que o Projeto Habilita

**Análise de Vendas Consolidada ERP + E-commerce:** Visão unificada de vendas (Bling ERP + WooCommerce). Permite receita consolidada, comparação de performance entre canais, análise de sazonalidade, segmentação de clientes e ticket médio por canal.

**Taxa de Cancelamento por Canal:** Percentual de pedidos cancelados por origem (Bling vs WooCommerce). Identifica diferenças operacionais, correlaciona cancelamentos com fatores específicos e compara padrões entre canais.

**Lead Time: Pedido (WooCommerce) → Faturamento (Bling):** Tempo entre criação do pedido e faturamento. Permite identificar gargalos, garantir SLA, analisar tendências e alertar sobre lead times acima do esperado.

**Divergências de Status e Reconciliação:** Identificação de inconsistências entre status em Bling vs WooCommerce. Analisa volume de divergências, padrões, tempo de resolução, impacto financeiro e taxa de reconciliação automática vs manual.

## Métricas Recomendadas

**Volume Diário de Pedidos Bling Carregados:** Quantidade de pedidos inseridos em `pedidos_bling` por data. Monitora volume processado, identifica dias anormais e valida completude.

**Taxa de Erro por Data:** Percentual de pedidos que falharam durante processamento. Fórmula: `(pedidos_com_erro / pedidos_totais) * 100`. Identifica datas problemáticas e valida qualidade do pipeline.

**Tempo de Execução por Data:** Duração total do processamento (início ao fim). Monitora performance, identifica degradação e estima tempo para backfill histórico.

**Taxa de Match Bling ↔ WooCommerce:** Percentual de pedidos do Bling unificados com WooCommerce. Valida qualidade da integração e identifica pedidos órfãos. Interpretação: >90% (bom), <70% (possível problema), tendência decrescente (sincronização ou mudança nos dados).

## Dashboard Recomendado

Visão consolidada: volume diário (linha), taxa de erro (barras), tempo de execução (linha), taxa de match (KPI), alertas para métricas fora do esperado. Atualização diária após processamento de cada data.
