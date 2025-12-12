## Tabela Virtual Mitra (HTML standalone)

### Descrição
Componente HTML de tabela com virtual scroll para grandes volumes (50k+ linhas), suporte a colunas editáveis, botões de ação, alertas visuais, totalizadores e cabeçalhos pivotados por FK, espelhando o comportamento do `general_lista.jsx` sem depender de React.

### Configuração

#### Variáveis do componentData

| Variável | Tipo | Obrigatório | Descrição | Exemplo |
|----------|------|-------------|-----------|---------|
| `query` | string | Sim | SQL de dados principal (SELECT) | `"SELECT * FROM TAREFAS"` |
| `jdbcId` | string/number | Não (default 1) | ID do datasource JDBC | `"1"` |
| `columns` | string (JSON) | Sim | Lista de colunas (vide modelo abaixo) | `"[]"` |
| `iconPhotoColumnsConfig` | string (JSON) | Não | Colunas de ícone/foto | `[{"columnName":"FOTO","imageField":"URL"}]` |
| `buttonsGroupConfig` | string (JSON) | Não | Grupo de botões na coluna de ações | `[{"icon":"Eye","interactionColumn":"INTERACAO"}]` |
| `headerButtons` | string (JSON) | Não | Botões no cabeçalho (estático) | `[{"label":"Novo","icon":"Plus","interaction":"form:10"}]` |
| `headerButtonsQuery` | string | Não | Query que retorna interações dinâmicas dos botões (uma linha) | `"SELECT ..."` |
| `tamanhoModal` | string | Não | Tamanho padrão de modais `LARGURA:ALTURA` | `"80:90"` |
| `closeModalOnReload` | bool/string | Não | Fecha modal ao recarregar tabela | `"false"` |
| `enableSearch` | bool/string | Não | Exibe busca global | `"true"` |
| `variableSearch` | string | Não | Nome da variável Mitra para sincronizar busca | `":VAR_BUSCA"` |
| `tableDesign` | string | Não | `"classic"` ou `"data"` | `"data"` |
| `xAxisFkColumn` | string | Não | Coluna FK para pivô em grupos | `"ID_GRUPO"` |
| `xAxisLabelColumn` | string | Não | Rótulo exibido no grupo (fallback = FK) | `"NOME_GRUPO"` |
| `xAxisRowKeyColumn` | string | Não | Chave de linha no modo pivô | `"ID_LINHA"` |
| `xAxisFixedColumns` | string/JSON | Não | Colunas fixas (array) | `["ID","NOME"]` |
| `xAxisDetailColumns` | string/JSON | Não | Colunas detalhe (array) | `["VALOR","STATUS"]` |
| `showBorder` | bool/string | Não | Controla borda do container | `"true"` |
| `headerButtonsQuery` | string | Não | Query para preencher interações dos botões de topo | `"SELECT ..."` |
| `setRowIdForChangeDBAction` | string | Não | Variável Mitra para ID da linha em updates | `":VAR_ID"` |
| `onValueChangeMitraVariable` | string | Não | Variável Mitra para valor alterado (inputs) | `":VAR_VALOR"` |

**Observação:** Todas as variáveis chegam como string. Use `JSON.parse()` para objetos/arrays e `parseInt()` para números.

#### Modelo de `columns` (JSON)
```json
[
  {
    "columnName": "ID",
    "dataField": "ID",
    "columnType": "data_number",
    "width": "80px",
    "enableSorting": true
  },
  {
    "columnName": "NOME",
    "dataField": "NOME",
    "columnType": "data_text",
    "enableSorting": true
  },
  {
    "columnName": "STATUS",
    "dataField": "STATUS",
    "columnType": "input_dropdown",
    "dropdownOptions": [
      { "label": "Aberto", "value": "ABERTO" },
      { "label": "Fechado", "value": "FECHADO" }
    ],
    "onValueChangeDBActionID": 12,
    "onValueChangeMitraVariable": ":VAR_STATUS"
  },
  {
    "columnName": "DETALHE",
    "dataField": "INTERACAO",
    "columnType": "action_button",
    "buttonText": "Abrir",
    "interactionColumn": "INTERACAO",
    "mitraModalWidthForButton": 80,
    "mitraModalHeightForButton": 90
  }
]
```

#### Colunas especiais
- **data_iconphoto**: use `imageField` (URL), `iconField` (nome do ícone), `iconColorField`, `iconBgColor1Field`, `iconBgColor2Field`, `imageShape` (`circle|rounded|square`).
- **action_buttons_group**: configure em `buttonsGroupConfig` (array de botões com `interactionColumn`).
- **input_dropdown**: pode usar `dropdownOptionsWithQuery` para carregar opções dinamicamente (`SELECT id, label FROM ...`).

### Dependências

#### Funções Mitra Utilizadas
- `queryMitra()` — busca dados principais e opções de dropdown.
- `setVariableMitra()` — sincroniza ID da linha e novos valores.
- `dbactionMitra()` — executa updates (checkbox/input/dropdown) e ações de botões.
- `formMitra()` — abre forms com `contentId` (ID da linha).
- `modalMitra()` / `safeModalMitra()` — abre telas em modal com tamanho configurável.
- `actionMitra()` — executa automações completas.

#### Variáveis Globais
- `:VAR_ID` (exemplo) — ID da linha para updates (defina em `setRowIdForChangeDBAction`).
- `:VAR_VALOR` (exemplo) — valor alterado (defina em `onValueChangeMitraVariable`).
- `:VAR_BUSCA` (opcional) — valor da busca global (`variableSearch`).

#### DBActions / Actions
- Defina os IDs no `componentData` (por exemplo `onValueChangeDBActionID`, interações `dbaction:123` em colunas/botões).
- Se precisar de novas DBActions, documente SQL, variáveis `:VAR_*` e operação (INSERT/UPDATE/DELETE).

### Exemplos

#### Exemplo Básico (somente leitura)
```json
{
  "query": "SELECT ID, NOME, STATUS FROM TAREFAS",
  "columns": "[{\"columnName\":\"ID\",\"dataField\":\"ID\",\"columnType\":\"data_number\"},{\"columnName\":\"NOME\",\"dataField\":\"NOME\",\"columnType\":\"data_text\"},{\"columnName\":\"STATUS\",\"dataField\":\"STATUS\",\"columnType\":\"data_text\"}]",
  "enableSearch": "true",
  "tableDesign": "classic"
}
```

#### Exemplo com Edição e Botões
```json
{
  "query": "SELECT ID, NOME, STATUS, INTERACAO FROM TAREFAS",
  "columns": "[{\"columnName\":\"STATUS\",\"dataField\":\"STATUS\",\"columnType\":\"input_dropdown\",\"dropdownOptions\":[{\"label\":\"Aberto\",\"value\":\"AB\"},{\"label\":\"Fechado\",\"value\":\"FE\"}],\"onValueChangeDBActionID\":12,\"onValueChangeMitraVariable\":\":VAR_STATUS\"},{\"columnName\":\"Ação\",\"dataField\":\"INTERACAO\",\"columnType\":\"action_button\",\"interactionColumn\":\"INTERACAO\",\"buttonText\":\"Abrir\"}]",
  "setRowIdForChangeDBAction": ":VAR_ID",
  "onValueChangeMitraVariable": ":VAR_VALOR",
  "tamanhoModal": "80:90",
  "enableSearch": "true"
}
```

#### Exemplo Pivô (subgrupos)
```json
{
  "query": "SELECT ID_LINHA, ID_GRUPO, NOME_GRUPO, METRICA, STATUS FROM VW_PIVO",
  "columns": "[{\"columnName\":\"METRICA\",\"dataField\":\"METRICA\",\"columnType\":\"data_number\"},{\"columnName\":\"STATUS\",\"dataField\":\"STATUS\",\"columnType\":\"data_text\"}]",
  "xAxisFkColumn": "ID_GRUPO",
  "xAxisLabelColumn": "NOME_GRUPO",
  "xAxisRowKeyColumn": "ID_LINHA",
  "xAxisFixedColumns": "[\"ID_LINHA\"]",
  "xAxisDetailColumns": "[\"METRICA\",\"STATUS\"]"
}
```

### Notas Importantes
- Virtual scroll assume altura fixa ~44px por linha; se células muito altas, ajuste `rowHeight` no código.
- Busca global sincroniza com `variableSearch` se configurada; caso contrário, filtra só no cliente.
- Ordenação suporta números (pt-BR) e datas (ISO ou dd/MM/yyyy simples).
- Interações devem seguir o formato `tipo:ID` (`dbaction:10`, `form:20`, `modal:30`, `action:40`).
- Nunca use navegação direta (`window.location`); sempre `modalMitra`/`actionMitra`.
- Todas as strings no `componentData` devem ser válidas (JSON para arrays/objetos).

