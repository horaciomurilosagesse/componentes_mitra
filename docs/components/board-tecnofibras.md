# Board Tecnofibras - Workflow de Processos

## Descrição

Componente Kanban para gerenciamento de processos de engenharia e compras da empresa Tecnofibras, especializada em montagem de veículos. O board permite visualizar, criar e gerenciar solicitações de orçamento através de um fluxo de aprovação visual.

O componente exibe:
- **KPIs** de solicitações (fechadas, pendentes, perdidas)
- **Status das cotações** (em dia, em atraso, crítico)
- **Prioridades** (baixa, média, alta)
- **Colunas Kanban** configuráveis com cards arrastáveis
- **Filtros** por coluna e busca global
- **Modal de criação** de novas solicitações

## Configuração

### Variáveis do componentData

Todas as variáveis chegam como **string**. Use `parseInt()` para números e `JSON.parse()` para objetos JSON quando necessário.

| Variável | Tipo | Obrigatório | Descrição | Exemplo |
|----------|------|-------------|-----------|---------|
| `colunasKanban` | string | Sim | Query SQL que retorna as colunas do kanban. Deve retornar: ID, DESCRICAO, COR_HEX, ICONE | `"SELECT ID, DESCRICAO, COR_HEX, ICONE FROM FASES_KANBAN ORDER BY ORDEM"` |
| `cardsKanban` | string | Sim | Query SQL que retorna os cards do kanban. Deve retornar: ID, TITULO, PRIORIDADE, ID_FASE, CONTATO, NUM_RFQ, NUM_SO, DATA_RETORNO, DATA_RETORNO_FMT, STATUS, ID_TELA_MODAL | `"SELECT ID, TITULO, PRIORIDADE, ID_FASE, CONTATO, NUM_RFQ, NUM_SO, DATA_RETORNO, DATA_RETORNO_FMT, STATUS, ID_TELA_MODAL FROM SOLICITACOES"` |
| `queryGestores` | string | Sim | Query SQL que retorna os gestores para o dropdown. Deve retornar: ID, NOME | `"SELECT ID, NOME FROM GESTORES WHERE ATIVO = 1 ORDER BY NOME"` |
| `idActionSalvar` | string | Sim | ID numérico da DBAction que salva uma nova solicitação | `"5"` |
| `actionDeleteId` | string | Não | ID numérico da Action que exclui uma solicitação | `"12"` |
| `screenModalId` | string | Não | ID numérico da tela modal para visualizar detalhes do card | `"2"` |
| `kpiDiasCriticoAtraso` | string | Não | Número de dias de atraso para considerar crítico (padrão: 7) | `"7"` |
| `kpiDiasEmDia` | string | Não | Número de dias para considerar "em dia" (padrão: 3) | `"3"` |

**Observação:** Todas as variáveis chegam como string. O componente faz a conversão automaticamente usando a função `getComponentDataValue()`.

**Exemplo de configuração no editor Mitra:**
```json
{
  "colunasKanban": "SELECT ID, DESCRICAO, COR_HEX, ICONE FROM FASES_KANBAN ORDER BY ORDEM",
  "cardsKanban": "SELECT ID, TITULO, PRIORIDADE, ID_FASE, CONTATO, NUM_RFQ, NUM_SO, DATA_RETORNO, DATA_RETORNO_FMT, STATUS, ID_TELA_MODAL FROM SOLICITACOES WHERE ATIVO = 1",
  "queryGestores": "SELECT ID, NOME FROM GESTORES WHERE ATIVO = 1 ORDER BY NOME",
  "idActionSalvar": "5",
  "actionDeleteId": "12",
  "screenModalId": "2",
  "kpiDiasCriticoAtraso": "7",
  "kpiDiasEmDia": "3"
}
```

**Exemplo de acesso no código:**
```javascript
// O componente já faz a conversão automaticamente
const actionId = getComponentDataValue('idActionSalvar', null, 'number');
const diasCritico = getComponentDataValue('kpiDiasCriticoAtraso', 7, 'number');
```

## Dependências

### Funções Mitra Utilizadas

- **`queryMitra(query)`** - Busca colunas, cards e gestores do banco de dados
  - Usado em: `fetchColumns()`, `fetchCards()`, `populateDropdowns()`
  
- **`setVariableMitra({ name, content })`** - Define variáveis globais antes de ações
  - Usado em: `abrirModalCard()`, `excluirCard()`, `criarNovoCard()`
  - Variáveis utilizadas:
    - `:VAR_CARD_ID` - ID do card para visualização/exclusão
    - `:VAR_DESCRICAO` - Descrição da solicitação
    - `:VAR_GESTOR_ID` - ID do gestor responsável
    - `:VAR_PRIORIDADE` - Prioridade (Baixa, Média, Alta)
    - `:VAR_NUM_SO` - Número da SO
    - `:VAR_DATA` - Data da solicitação
    - `:VAR_CLIENTE` - Nome do cliente
    - `:VAR_TIPO_CLIENTE` - Tipo de cliente (Atual/Novo)
    - `:VAR_CONTATO` - Contato do cliente
    - `:VAR_EMAIL` - E-mail do cliente
    - `:VAR_D_RETORNO` - Data de retorno esperada
    - `:VAR_NUM_RFQ` - Número RFQ
    - `:VAR_ACORDO_CONF` - Acordo de confidencialidade (Sim/Não)
    - `:VAR_MANUAL_QUALIDADE` - Manual de qualidade (Sim/Não)
    - `:VAR_COND_PAG_PECA` - Condição de pagamento peça
    - `:VAR_COND_PAG_FERRAMENTAL` - Condição de pagamento ferramental
    - `:VAR_COND_ENTREGA` - Condição de entrega
    - `:VAR_SOP` - Data SOP

- **`dbactionMitra(id)`** - Executa DBAction para salvar nova solicitação
  - Usado em: `criarNovoCard()`
  - Recebe apenas o ID numérico (não objeto)

- **`actionMitra({ id })`** - Executa Action para excluir solicitação
  - Usado em: `excluirCard()`
  - Recebe objeto com propriedade `id`

- **`modalMitra({ id, width, height })`** - Abre tela modal para visualizar detalhes
  - Usado em: `abrirModalCard()`

### DBActions Necessárias

#### DBAction de Salvar Solicitação

- **ID:** Configurado em `componentData.idActionSalvar`
- **Descrição:** Insere uma nova solicitação de orçamento no banco de dados
- **Variáveis Utilizadas:**
  - `:VAR_DESCRICAO`
  - `:VAR_GESTOR_ID`
  - `:VAR_PRIORIDADE`
  - `:VAR_NUM_SO`
  - `:VAR_DATA`
  - `:VAR_CLIENTE`
  - `:VAR_TIPO_CLIENTE`
  - `:VAR_CONTATO`
  - `:VAR_EMAIL`
  - `:VAR_D_RETORNO`
  - `:VAR_NUM_RFQ`
  - `:VAR_ACORDO_CONF`
  - `:VAR_MANUAL_QUALIDADE`
  - `:VAR_COND_PAG_PECA`
  - `:VAR_COND_PAG_FERRAMENTAL`
  - `:VAR_COND_ENTREGA`
  - `:VAR_SOP`
- **Operação:** INSERT

**Exemplo de SQL (ajuste conforme sua estrutura de tabela):**
```sql
INSERT INTO SOLICITACOES (
    DESCRICAO, GESTOR_ID, PRIORIDADE, NUM_SO, DATA, 
    CLIENTE, TIPO_CLIENTE, CONTATO, EMAIL, DATA_RETORNO, 
    NUM_RFQ, ACORDO_CONF, MANUAL_QUALIDADE, 
    COND_PAG_PECA, COND_PAG_FERRAMENTAL, COND_ENTREGA, SOP
) VALUES (
    :VAR_DESCRICAO, :VAR_GESTOR_ID, :VAR_PRIORIDADE, :VAR_NUM_SO, :VAR_DATA,
    :VAR_CLIENTE, :VAR_TIPO_CLIENTE, :VAR_CONTATO, :VAR_EMAIL, :VAR_D_RETORNO,
    :VAR_NUM_RFQ, :VAR_ACORDO_CONF, :VAR_MANUAL_QUALIDADE,
    :VAR_COND_PAG_PECA, :VAR_COND_PAG_FERRAMENTAL, :VAR_COND_ENTREGA, :VAR_SOP
)
```

### Actions Necessárias

#### Action de Excluir Solicitação

- **ID:** Configurado em `componentData.actionDeleteId`
- **Descrição:** Exclui uma solicitação do banco de dados
- **Variáveis Utilizadas:**
  - `:VAR_CARD_ID` - ID da solicitação a ser excluída
- **Operação:** DELETE (via DBAction dentro da Action)

**Nota:** A Action deve:
1. Receber `:VAR_CARD_ID` definida pelo componente
2. Executar uma DBAction de DELETE que remove o registro
3. Opcionalmente, atualizar o componente após exclusão

### Variáveis Globais Utilizadas

- **`:VAR_CARD_ID`** - ID do card/solicitação
  - Definida antes de abrir modal de detalhes
  - Definida antes de excluir card
  - Usada pela tela modal para carregar dados do card

- **`:VAR_DESCRICAO` até `:VAR_SOP`** - Dados do formulário de criação
  - Definidas antes de executar DBAction de salvar
  - Usadas pela DBAction para inserir nova solicitação

### Telas Utilizadas

- **ID:** Configurado em `componentData.screenModalId` (ou `ID_TELA_MODAL` do card)
- **Uso:** Modal para visualizar/editar detalhes do card
- **Contexto:** Abre quando usuário clica em um card do kanban
- **Variável de Contexto:** `:VAR_CARD_ID` é definida antes de abrir

## Estrutura de Dados

### Query de Colunas (`colunasKanban`)

A query deve retornar as seguintes colunas:

| Coluna | Tipo | Descrição |
|--------|------|-----------|
| `ID` | number | ID único da coluna/fase |
| `DESCRICAO` | string | Nome da coluna (ex: "Em Análise", "Aprovado") |
| `COR_HEX` | string | Cor hexadecimal para o topo da coluna (ex: "#002855") |
| `ICONE` | string | Nome do ícone Phosphor (ex: "folder-simple", "check-circle") |

**Exemplo de resultado:**
```
ID | DESCRICAO    | COR_HEX  | ICONE
1  | Em Análise   | #002855  | folder-simple
2  | Aprovado     | #10B981  | check-circle
3  | Rejeitado    | #EF4444  | x-circle
```

### Query de Cards (`cardsKanban`)

A query deve retornar as seguintes colunas:

| Coluna | Tipo | Descrição |
|--------|------|-----------|
| `ID` | number | ID único do card/solicitação |
| `TITULO` | string | Título/descrição do card |
| `PRIORIDADE` | string | Prioridade: "Baixa", "Média" ou "Alta" |
| `ID_FASE` | number | ID da coluna onde o card deve aparecer |
| `CONTATO` | string | Nome do contato (opcional) |
| `NUM_RFQ` | string | Número RFQ (opcional) |
| `NUM_SO` | string | Número SO (opcional) |
| `DATA_RETORNO` | date | Data de retorno esperada (opcional) |
| `DATA_RETORNO_FMT` | string | Data de retorno formatada para exibição (opcional) |
| `STATUS` | string | Status da solicitação (para cálculo de KPIs) |
| `ID_TELA_MODAL` | number | ID da tela modal para este card (opcional, usa `screenModalId` como fallback) |

**Exemplo de resultado:**
```
ID | TITULO           | PRIORIDADE | ID_FASE | CONTATO    | NUM_RFQ | STATUS
1  | Orçamento Peça X | Alta       | 1       | João Silva | RFQ001  | Pendente
2  | Orçamento Peça Y | Média      | 2       | Maria      | RFQ002  | Fechada
```

### Query de Gestores (`queryGestores`)

A query deve retornar as seguintes colunas:

| Coluna | Tipo | Descrição |
|--------|------|-----------|
| `ID` | number | ID único do gestor |
| `NOME` | string | Nome completo do gestor |

## Exemplos

### Exemplo Básico

Configuração mínima necessária no editor Mitra:

```json
{
  "colunasKanban": "SELECT ID, DESCRICAO, COR_HEX, ICONE FROM FASES_KANBAN ORDER BY ORDEM",
  "cardsKanban": "SELECT ID, TITULO, PRIORIDADE, ID_FASE, CONTATO, NUM_RFQ, NUM_SO, DATA_RETORNO, DATA_RETORNO_FMT, STATUS, NULL as ID_TELA_MODAL FROM SOLICITACOES WHERE ATIVO = 1",
  "queryGestores": "SELECT ID, NOME FROM GESTORES WHERE ATIVO = 1 ORDER BY NOME",
  "idActionSalvar": "5",
  "actionDeleteId": "12",
  "screenModalId": "2"
}
```

### Exemplo Avançado

Com configurações de KPIs personalizadas:

```json
{
  "colunasKanban": "SELECT ID, DESCRICAO, COR_HEX, ICONE FROM FASES_KANBAN ORDER BY ORDEM",
  "cardsKanban": "SELECT ID, TITULO, PRIORIDADE, ID_FASE, CONTATO, NUM_RFQ, NUM_SO, DATA_RETORNO, DATE_FORMAT(DATA_RETORNO, '%d/%m/%Y') as DATA_RETORNO_FMT, STATUS, ID_TELA_MODAL FROM SOLICITACOES WHERE ATIVO = 1",
  "queryGestores": "SELECT ID, NOME FROM GESTORES WHERE ATIVO = 1 ORDER BY NOME",
  "idActionSalvar": "5",
  "actionDeleteId": "12",
  "screenModalId": "2",
  "kpiDiasCriticoAtraso": "10",
  "kpiDiasEmDia": "5"
}
```

### Exemplo de Query com JOIN

Query de cards com informações relacionadas:

```sql
SELECT 
    s.ID,
    s.TITULO,
    s.PRIORIDADE,
    s.ID_FASE,
    c.NOME as CONTATO,
    s.NUM_RFQ,
    s.NUM_SO,
    s.DATA_RETORNO,
    DATE_FORMAT(s.DATA_RETORNO, '%d/%m/%Y') as DATA_RETORNO_FMT,
    s.STATUS,
    s.ID_TELA_MODAL
FROM SOLICITACOES s
LEFT JOIN CONTATOS c ON s.CONTATO_ID = c.ID
WHERE s.ATIVO = 1
ORDER BY s.DATA_CRIACAO DESC
```

## Funcionalidades

### KPIs Dinâmicos

O componente calcula automaticamente três tipos de KPIs:

1. **Solicitações:**
   - Fechadas: Status contém "fechad" ou "concluíd"
   - Pendentes: Qualquer outro status
   - Perdidas: Status contém "perdid" ou "cancelad"

2. **Status das Cotações:**
   - Em dia: Data de retorno até X dias no futuro (configurável via `kpiDiasEmDia`)
   - Em atraso: Data de retorno passada, mas menos de X dias (configurável via `kpiDiasCriticoAtraso`)
   - Crítico: Data de retorno passada há mais de X dias (configurável via `kpiDiasCriticoAtraso`)

3. **Prioridades:**
   - Baixa, Média, Alta: Contagem por valor do campo `PRIORIDADE`

### Filtros

- **Filtro Global:** Busca em todos os cards de todas as colunas
- **Filtro por Coluna:** Busca apenas nos cards da coluna específica
- **Debounce:** Aplicado automaticamente (300ms) para melhorar performance

### Criação de Cards

O modal de criação permite preencher:
- Descrição (obrigatório)
- Gestor (obrigatório, carregado do banco)
- Prioridade (obrigatório: Baixa, Média, Alta)
- Nº SO, Data
- Cliente (obrigatório)
- Tipo de Cliente (obrigatório: Atual/Novo)
- Contato, E-mail
- Data Retorno, Nº RFQ
- Acordo de Confidencialidade (obrigatório)
- Manual de Qualidade (obrigatório)
- Condições de Pagamento (Peça e Ferramental)
- Condição de Entrega
- SOP

### Exclusão de Cards

- Botão de exclusão aparece ao passar o mouse sobre o card
- Confirmação antes de excluir
- Executa Action configurada em `actionDeleteId`

## Notas Importantes

### Tratamento de componentData

- **Todas as propriedades chegam como string** - O componente faz conversão automática usando `getComponentDataValue()`
- Validação de campos obrigatórios antes de usar
- Fallbacks para valores não configurados

### Navegação e Iframes

- **Nunca use** `<a href>` ou `window.location` - Quebra a estrutura de navegação
- **Sempre use** `modalMitra()` para abrir telas modais
- **Sempre use** `actionMitra()` para executar fluxos
- **Sempre defina** variáveis com `setVariableMitra()` antes de navegar

### Performance

- Debounce aplicado em filtros (300ms)
- Queries devem ser otimizadas (usar LIMIT se necessário)
- Skeleton loaders durante carregamento

### Validações

- Formulário valida campos obrigatórios antes de salvar
- Validação de e-mail se preenchido
- Mensagens de erro descritivas
- Validação de componentData na inicialização

### Tratamento de Erros

- Mensagens de erro descritivas com sugestões de solução
- Fallbacks para quando `componentData` não está disponível
- Logs detalhados no console para debugging
- Toast notifications para feedback ao usuário

## Troubleshooting

### Board não carrega

**Problema:** Tela em branco ou mensagem de erro

**Soluções:**
1. Verifique se todas as variáveis obrigatórias estão configuradas no editor Mitra
2. Confirme se as queries SQL estão corretas e retornam dados
3. Verifique o console do navegador para erros específicos
4. Confirme que `componentData` está disponível (aguarde até 5 segundos)

### Cards não aparecem nas colunas

**Problema:** Colunas aparecem mas sem cards

**Soluções:**
1. Verifique se `ID_FASE` dos cards corresponde ao `ID` das colunas
2. Confirme se a query `cardsKanban` retorna a coluna `ID_FASE`
3. Verifique se há cards no banco de dados com status ativo

### KPIs mostram zero

**Problema:** Todos os KPIs mostram 0

**Soluções:**
1. Verifique se a query `cardsKanban` retorna dados
2. Confirme se os campos `STATUS` e `PRIORIDADE` estão sendo retornados
3. Verifique se `DATA_RETORNO` está no formato correto (date)

### Modal de detalhes não abre

**Problema:** Clicar no card não abre modal

**Soluções:**
1. Verifique se `screenModalId` está configurado no `componentData`
2. Confirme se o ID da tela existe na plataforma Mitra
3. Verifique se `modalMitra` está disponível (função nativa do Mitra)
4. Verifique o console para erros específicos

### Erro ao criar card

**Problema:** Formulário não salva

**Soluções:**
1. Verifique se `idActionSalvar` está configurado
2. Confirme se a DBAction existe e está correta
3. Verifique se todas as variáveis `:VAR_*` estão sendo usadas na DBAction
4. Verifique se os campos obrigatórios estão preenchidos
5. Confirme se o gestor foi selecionado (dropdown populado)

### Dropdown de gestores vazio

**Problema:** Dropdown não mostra opções

**Soluções:**
1. Verifique se `queryGestores` está configurada
2. Confirme se a query retorna dados (teste no banco)
3. Verifique se a query retorna colunas `ID` e `NOME`
4. Verifique o console para erros de query
