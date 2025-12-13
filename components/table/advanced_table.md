## Tabela Avançada (HTML Puro)

### Descrição
Tabela HTML completa com ordenação, filtro com debounce, redimensionamento de colunas por arraste, scroll infinito, totalizadores, paginação opcional e integração total com ações Mitra (modal, form, dbaction, action). Usa apenas HTML/CSS/JavaScript puro, seguindo o padrão do Mitra para `componentData` e funções nativas.

### Configuração

#### Variáveis do componentData

| Variável | Tipo | Obrigatório | Descrição | Exemplo |
|----------|------|-------------|-----------|---------|
| `title` | string | Não | Título exibido no topo | `"Minha Tabela"` |
| `subtitle` | string | Não | Subtítulo/descrição | `"Resumo de registros"` |
| `query` | string | Sim | SQL de consulta principal | `"SELECT * FROM CLIENTES"` |
| `jdbcId` | string/number | Não | ID do datasource JDBC (default `1`) | `"2"` |
| `columns` | string (JSON) | Sim | Configuração das colunas (ver abaixo) | `"[{...}]"` |
| `headerButtons` | string (JSON) | Não | Botões do cabeçalho com ações Mitra | `"[{\"label\":\"Novo\",\"action\":\"form:10\"}]"` |
| `enableSearch` | string/bool | Não | Ativa o campo de busca (`true`/`false`) | `"true"` |
| `enablePagination` | string/bool | Não | Exibe botões Anterior/Próxima; se `false`, só scroll infinito | `"true"` |
| `pageSize` | string/number | Não | Lote de paginação legada (fallback para `renderBatchSize`) | `"100"` |
| `renderBatchSize` | string/number | Não | Tamanho do lote renderizado por scroll (default `200`) | `"200"` |
| `enableTotals` | string/bool | Não | Habilita totalizadores configurados | `"true"` |
| `totalsConfig` | string (JSON) | Não | Lista de totalizadores `{ dataField, type: "sum"|"avg" }` | `"[{\"dataField\":\"VALOR\",\"type\":\"sum\"}]"` |
| `searchVariable` | string | Não | Nome da variável Mitra para sincronizar busca | `":VAR_BUSCA"` |
| `rowIdVariable` | string | Não | Variável que recebe o ID da linha antes de ações | `":VAR_ROW_ID"` |

**Observação:** Todas as propriedades de `componentData` chegam como **string**. Converta números com `Number()` e objetos JSON com `JSON.parse()`.

#### Estrutura de `columns`

Cada item aceita:
- `columnName` (string) — nome interno
- `headerName` (string) — rótulo exibido
- `dataField` (string) — campo retornado na query
- `columnType` (string) — um dos: `data_text`, `data_number`, `data_boolean_checkbox`, `input_text`, `input_number`, `input_date`, `input_dropdown`, `data_iconphoto`, `action_button`, `action_buttons_group`
- `width` (string) — ex.: `"140px"`
- `minWidth` (string) — ex.: `"80px"`
- `resizable` (bool) — permite arrastar borda
- `sortable` (bool) — permite ordenar
- `alignment` (string) — `left` | `center` | `right`
- `dropdownOptions` (array) ou `dropdownOptionsQuery` (string SQL)
- `buttons` (array) para `action_buttons_group` com `{ icon?, hoverText?, action, bgColor?, textColor? }`
- `onChangeAction` (string) — ação Mitra no formato `tipo:id`
- `inputDateFormat` (string) — formatação custom de entrada, se necessário

Exemplo mínimo:
```json
{
  "columns": "[{\"columnName\":\"ID\",\"dataField\":\"ID\",\"columnType\":\"data_number\",\"width\":\"80px\",\"sortable\":true},{\"columnName\":\"NOME\",\"dataField\":\"NOME\",\"columnType\":\"data_text\"},{\"columnName\":\"ATIVO\",\"dataField\":\"ATIVO\",\"columnType\":\"data_boolean_checkbox\",\"onChangeAction\":\"dbaction:123\"},{\"columnName\":\"STATUS\",\"dataField\":\"STATUS\",\"columnType\":\"input_dropdown\",\"dropdownOptionsQuery\":\"SELECT ID, NOME FROM STATUS\"},{\"columnName\":\"ACOES\",\"columnType\":\"action_buttons_group\",\"buttons\":[{\"hoverText\":\"Visualizar\",\"action\":\"modal:10\"},{\"hoverText\":\"Editar\",\"action\":\"form:20\"},{\"hoverText\":\"Excluir\",\"action\":\"dbaction:30\",\"bgColor\":\"#ef4444\"}]}]"
}
```

#### Totais
`totalsConfig` é um array de objetos `{ dataField, type }`, onde `type` pode ser `"sum"` ou `"avg"`. Somente campos numéricos são considerados; valores inválidos são ignorados.

### Dependências

#### Funções Mitra utilizadas
- `queryMitra({ query, jdbcId })` — busca de dados e opções dinâmicas
- `setVariableMitra({ name, content })` — variável de linha (ex.: `:VAR_ROW_ID`) e busca
- `dbactionMitra({ id })` — ações de escrita
- `formMitra({ id, contentId })` — abertura de formulário com o ID da linha
- `modalMitra({ id, width, height })` — abertura de tela em modal
- `actionMitra({ id })` — execução de automações

#### Variáveis globais utilizadas
- `:VAR_ROW_ID` (configurável via `rowIdVariable`) — recebe o ID da linha antes de qualquer ação
- `:VAR_BUSCA` (opcional, via `searchVariable`) — sincroniza o termo de busca atual

### Exemplos

#### Exemplo Básico
**Descrição:** Tabela simples com busca, ordenação e botão de novo registro.
```json
{
  "title": "Clientes",
  "subtitle": "Lista de clientes ativos",
  "query": "SELECT ID, NOME, EMAIL, ATIVO FROM CLIENTES",
  "columns": "[{\"columnName\":\"ID\",\"dataField\":\"ID\",\"columnType\":\"data_number\",\"sortable\":true},{\"columnName\":\"NOME\",\"dataField\":\"NOME\",\"columnType\":\"data_text\"},{\"columnName\":\"EMAIL\",\"dataField\":\"EMAIL\",\"columnType\":\"data_text\"},{\"columnName\":\"ATIVO\",\"dataField\":\"ATIVO\",\"columnType\":\"data_boolean_checkbox\",\"onChangeAction\":\"dbaction:101\"}]",
  "headerButtons": "[{\"label\":\"Novo\",\"action\":\"form:201\",\"primary\":true}]"
}
```

#### Exemplo com Totais e Scroll Infinito
**Descrição:** Tabela de vendas com totalizador de valor e carregamento progressivo.
```json
{
  "title": "Vendas",
  "query": "SELECT ID, CLIENTE, VALOR, DATA FROM VENDAS",
  "renderBatchSize": "150",
  "enablePagination": "false",
  "enableTotals": "true",
  "totalsConfig": "[{\"dataField\":\"VALOR\",\"type\":\"sum\"}]",
  "columns": "[{\"columnName\":\"ID\",\"dataField\":\"ID\",\"columnType\":\"data_number\",\"width\":\"80px\"},{\"columnName\":\"CLIENTE\",\"dataField\":\"CLIENTE\",\"columnType\":\"data_text\",\"width\":\"200px\"},{\"columnName\":\"VALOR\",\"dataField\":\"VALOR\",\"columnType\":\"data_number\",\"width\":\"120px\"},{\"columnName\":\"DATA\",\"dataField\":\"DATA\",\"columnType\":\"input_date\",\"sortable\":true}]"
}
```

### Notas Importantes
- Use o padrão `tipo:id` para ações (`modal:10`, `form:20`, `dbaction:30`, `action:40`).
- Sempre envie o ID da linha para `rowIdVariable` antes das ações, já implementado pelo componente.
- Busca tem debounce de ~300ms e pode sincronizar com variável global (`searchVariable`).
- Redimensionamento de colunas é feito arrastando a borda direita do cabeçalho.
- Scroll infinito carrega mais linhas ao atingir ~80% do scroll; se `enablePagination=false`, nenhum botão aparece — apenas o carregamento incremental.
- Totalizadores operam em memória sobre os dados carregados; certifique-se de que a query retorne os campos numéricos necessários.

