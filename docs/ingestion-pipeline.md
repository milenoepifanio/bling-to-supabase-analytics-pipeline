# Pipeline de Ingestão

Pipeline de ingestão de pedidos do Bling. Processamento incremental dia a dia com controle de datas.

## Fluxo do Pipeline

### 1. Leitura de Credenciais
Busca token e refresh_token do Bling na tabela `integracoes` do Supabase (id = 1).

### 2. Identificação da Próxima Data
Consulta a tabela `bling_controle_datas` para encontrar a próxima data pendente (status diferente de ✅). Sempre processa a **data mais antiga pendente**.

### 3. Listagem de Pedidos do Dia
Chamada à API Bling (`GET /Api/v3/pedidos/vendas`) com `dataInicial` e `dataFinal` iguais à data selecionada. Retorna lista de IDs e números dos pedidos.

### 4. Busca de Detalhes por Pedido
Para cada pedido encontrado:
- Chamada à API Bling (`GET /Api/v3/pedidos/vendas/{id}`)
- **Retry automático** em caso de erro 429 (rate limit):
  - 3 tentativas máximas
  - Espera progressiva: 15s, 30s, 45s
- Aguarda **9 segundos** entre cada requisição para respeitar rate limit

### 5. Padronização de Campos
Aplica funções `safe_*` para normalização:
- `safe()` - Trata valores indefinidos ou vazios
- `safe_date()` - Valida e trata datas inválidas
- `safe_int()` - Converte para inteiro com tratamento de erros
- `safe_num()` - Converte para número decimal com tratamento de erros

### 6. Salvamento no Supabase
Inserção dos dados normalizados na tabela `pedidos_bling`.

### 7. Marcação de Data como Processada
Atualiza o status da data na tabela `bling_controle_datas` para ✅ após processar todos os pedidos do dia.

## Limitações Explícitas

### Processamento Síncrono
Pedidos são processados **sequencialmente** (um por vez). Não há processamento paralelo, garantindo respeito ao rate limit mas limitando throughput.

### Dependência de Rate Limit do Bling
Pipeline é limitado pela política de rate limiting da API Bling:
- **9 segundos** de espera obrigatória entre requisições de detalhes
- Impacto direto no tempo total de processamento (ex: 100 pedidos = ~15 minutos mínimo)

### Reinício Manual (Backfill)
Para processar múltiplas datas (backfill histórico):
- É necessário **reiniciar a execução** após cada data processada
- Reexecutar o pipeline para a próxima data
- Processo manual, não automatizado para múltiplas datas consecutivas

**Nota:** A automação via orquestrador resolve parcialmente essa limitação, mas ainda processa uma data por execução.