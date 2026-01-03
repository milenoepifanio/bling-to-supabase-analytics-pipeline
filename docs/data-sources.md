# Fontes de Dados

## Bling API v3

**Endpoints utilizados:**
- `GET /Api/v3/pedidos/vendas` - Lista vendas por data (dataInicial/dataFinal)
- `GET /Api/v3/pedidos/vendas/{id}` - Detalhes completos de um pedido específico

**Autenticação:** Bearer token (obtido da tabela `integracoes` no Supabase)
**Rate limiting:** 9 segundos entre requisições, retry automático para erros 429
**Dados coletados:** Pedidos com informações completas (cliente, pagamento, transporte, itens, etc.)

## Desafios Reais

### IDs Diferentes entre Bling e WooCommerce

Bling e WooCommerce utilizam sistemas de identificação distintos para os mesmos pedidos. A unificação ocorre no Supabase através de lógica SQL que identifica pedidos correspondentes (provavelmente via número do pedido, data, cliente, ou outros campos comuns).

### Regras de Status Diferentes

Cada plataforma possui seu próprio sistema de status de pedidos:
- **Bling:** Status específicos do ERP (ex: em aberto, faturado, cancelado)
- **WooCommerce:** Status do e-commerce (ex: pending, processing, completed, cancelled)

A normalização e mapeamento de status é feito na camada SQL do Supabase durante a unificação.

### Possíveis Atrasos de Sincronização
Pedidos podem aparecer no WooCommerce antes do faturamento no ERP (Bling), criando inconsistências temporárias. A lógica de unificação no Supabase precisa lidar com:
- Pedidos que existem em uma plataforma mas não na outra (temporariamente)
- Necessidade de reprocessamento ou atualização quando o pedido aparece na segunda plataforma
- Identificação de pedidos duplicados ou relacionados

**Solução:** Processamento incremental e lógica de unificação robusta na camada SQL, com possibilidade de reprocessamento quando novos dados chegam.