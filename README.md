# Importação de Pedidos Bling → Supabase

Pipeline de ingestão incremental de pedidos históricos do Bling para Supabase, criando uma base analítica unificada com o WooCommerce para inteligência de negócios.

## 📋 Sobre o Projeto

Este projeto realiza a importação e o processamento de pedidos históricos do **Bling** (ERP) para **Supabase**, processando os dados dia a dia de forma automatizada e controlada. Os dados são unificados no Supabase com o **WooCommerce** (e-commerce), gerando tabelas de dimensão e fato prontas para análises e métricas operacionais (receita, cancelamento, SLA).

> **⚠️ Importante:** A integração Bling + WooCommerce ocorre na camada SQL do Supabase. Isso ocorre porque o fluxo anterior foi implementado por outro analista via N8N, e a lógica de integração já está consolidada no Supabase.

## 🎯 Objetivo

Criar uma base única e confiável para pedidos e análises de vendas/operacionais, resolvendo o problema do processamento manual de históricos e da falta de controle sobre períodos processados.

## 🏗️ Arquitetura

### Fluxo de Processamento

1. Conectar ao Supabase
2. Buscar credenciais do Bling (`integracoes`)
3. Identificar a próxima data pendente (`bling_controle_datas`)
4. Listar vendas para a data (API Bling)
5. Processar cada pedido (detalhes, normalização, inserção)
6. Marcar data como processada (✅)

### Camadas no Supabase

- **Raw/Staging:** `pedidos_bling` (saída do pipeline)
- **Staging Views:** Padronização, limpeza, tipos, status
- **Integration Views:** Junção Bling ↔ WooCommerce
- **Marts:** Fatos e dimensões para consumo analítico

## 🛠️ Stack

- **Backend:** Python 3.10, cliente Python do Supabase, pandas, requests
- **Banco de dados:** PostgreSQL via Supabase
- **API:** Bling API v3
- **Infraestrutura:** Orquestração e automação (execução manual ou agendada)

## ✨ Funcionalidades

- ✅ Processamento incremental (dia a dia) com controle via `bling_controle_datas`
- ✅ Rate limiting (9s entre requisições, retry automático para 429)
- ✅ Tratamento robusto de dados (tipos, datas, nulos)
- ✅ Execução automatizada (manual ou agendada)
- ✅ Base analítica com tabelas de dimensão e fato prontas para BI

## 📊 Resultados

O projeto habilita:

- Análise consolidada de vendas (ERP + e-commerce)
- Taxa de cancelamento por canal
- Lead time: pedido (WooCommerce) → faturamento (Bling)
- Divergências de status e reconciliação

## 🔒 Confiabilidade

- Retry automático para erros 429 (rate limit)
- Controle de datas pendentes para processamento incremental
- Tratamento de nulos e tipos (`safe_*`)
- Idempotência via upsert no Supabase
- Constraints e chaves únicas para garantir integridade

## 📚 Documentação

Documentação completa disponível em `docs/`:

- **[Visão Geral](docs/overview.md)** - Visão geral do projeto, stack, arquitetura e fluxo
- **[Fontes de Dados](docs/data-sources.md)** - Bling API v3, WooCommerce e desafios de integração
- **[Pipeline de Ingestão](docs/ingestion-pipeline.md)** - Fluxo detalhado do pipeline e limitações
- **[Modelagem e Integração](docs/supabase-modeling-and-integration.md)** - Arquitetura em camadas, estratégias de matching e unificação
- **[Confiabilidade e Operações](docs/reliability-and-ops.md)** - Retry, idempotência, constraints e auditoria
- **[Resultados e Métricas](docs/results-and-measurement.md)** - Análises habilitadas e métricas recomendadas

## 📄 Licença

Este projeto foi desenvolvido como um estudo de caso de engenharia de dados.