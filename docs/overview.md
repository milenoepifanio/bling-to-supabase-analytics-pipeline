# Importação de Pedidos Bling - Visão Geral

Pipeline de ingestão de pedidos históricos do Bling para Supabase com processamento incremental. Dados unificados no Supabase com WooCommerce, criando tabelas dimensões e fatos prontas para BI e métricas operacionais (receita, cancelamento, SLA).

## Problema

Necessidade de base única e confiável para pedidos e análises. Processamento manual histórico é trabalhoso e propenso a erros. Falta de controle sobre períodos processados.

## Solução

Pipeline Python para ingestão incremental via API Bling. Processamento dia a dia com controle de datas no Supabase. **Integração Bling + WooCommerce ocorre na camada SQL do Supabase** (fluxo anterior via N8N por outro analista). Base analítica estruturada com dimensões e fatos.

## Principais Funcionalidades

- Processamento incremental (data a data) com controle via `bling_controle_datas`
- Rate limiting (9s entre requisições) e retry automático para 429
- Tratamento robusto de dados (validação de tipos, datas e nulos)
- Execução automatizada (manual ou agendada)
- Base analítica com tabelas de dimensão e fato prontas para BI

## Stack / Tecnologias

**Backend:** Python 3.10, Supabase Python client, pandas, requests

**Database:** PostgreSQL via Supabase. Tabelas: `integracoes`, `bling_controle_datas`, `pedidos_bling`. Views/funções SQL e CRON jobs para unificação Bling + WooCommerce (criação de `id_potencia`, remoção de duplicidades).

**Infraestrutura:** Orquestração e automação (execução manual ou agendada)

**API:** Bling API v3 com autenticação Bearer token e rate limiting

## Implementado

Conexão Supabase, busca de credenciais, processamento incremental de datas (sempre a mais antiga pendente), listagem de vendas por data, busca de detalhes completos, normalização de dados (safe functions), inserção em `pedidos_bling`, controle de status de datas, rate limiting (9s), retry automático para 429, execução automatizada, tratamento de erros.

## Arquitetura

**Separação de responsabilidades:**
- **Pipeline:** Ingestão de dados do Bling
- **Supabase (SQL):** Transformações, unificação Bling + WooCommerce, criação de `id_potencia`, remoção de duplicidades

**Fluxo:** Conectar Supabase → Buscar credenciais (`integracoes`) → Identificar próxima data pendente (`bling_controle_datas`) → Listar vendas (API Bling) → Processar cada pedido (detalhes, normalização, inserção) → Marcar data como processada (✅)

**Dados processados:** ID/número, datas (criação, saída, prevista), totais/descontos, cliente, situação, loja, pagamento, transporte, endereço, vendedor, intermediador, taxas, observações.