# Guia Prático: Configurar Dropdown com Salvamento no Backend Mitra

## O Que Foi Corrigido

Antes, quando você alterava um dropdown na tabela, o novo valor **não era enviado para o backend**. Agora, você pode configurar uma variável global que recebe o valor alterado antes da DBAction executar.

---

## Passo a Passo no Mitra

### Passo 1: Criar a DBAction no Mitra

1. **Acesse:** Menu de DBActions no Mitra
2. **Crie uma nova DBAction** (ou use uma existente)
3. **Anote o ID da DBAction** (exemplo: `123`)

**Exemplo de SQL da DBAction:**
```sql
UPDATE PEDIDOS
SET STATUS = :VAR_STATUS_NOVO
WHERE ID = :VAR_ROW_ID;
```

**Importante:**
- `:VAR_ROW_ID` = ID da linha que está sendo editada (já existe, não precisa criar)
- `:VAR_STATUS_NOVO` = novo valor do dropdown (você escolhe o nome)
- **Ambas as variáveis devem começar com `:VAR_`**

### Passo 2: Configurar a Coluna no ComponentData

No editor do componente HTML, na área `variables`, configure a coluna dropdown:

**Exemplo Completo:**
```json
{
  "title": "Pedidos",
  "query": "SELECT ID, CLIENTE, STATUS FROM PEDIDOS",
  "columns": "[{\"columnName\":\"ID\",\"dataField\":\"ID\",\"columnType\":\"data_number\",\"width\":\"80px\"},{\"columnName\":\"CLIENTE\",\"dataField\":\"CLIENTE\",\"columnType\":\"data_text\"},{\"columnName\":\"STATUS\",\"dataField\":\"STATUS\",\"columnType\":\"input_dropdown\",\"dropdownOptionsQuery\":\"SELECT ID, NOME FROM STATUS\",\"onChangeAction\":\"dbaction:123\",\"valueVariable\":\":VAR_STATUS_NOVO\"}]"
}
```

**O que cada campo faz:**
- `columnName`: Nome interno da coluna
- `dataField`: Campo retornado pela query (deve existir no SELECT)
- `columnType`: `"input_dropdown"` para dropdown editável
- `dropdownOptionsQuery`: Query SQL que retorna as opções (formato: `SELECT ID, NOME FROM TABELA`)
- `onChangeAction`: `"dbaction:123"` (onde `123` é o ID da sua DBAction)
- `valueVariable`: `":VAR_STATUS_NOVO"` (nome da variável que receberá o novo valor)

### Passo 3: Verificar rowIdVariable (Opcional)

Se você quiser usar um nome diferente para a variável do ID da linha:

```json
{
  "rowIdVariable": ":VAR_PEDIDO_ID"
}
```

**Padrão:** Se não definir, usa `:VAR_ROW_ID`

---

## Exemplo Completo Prático

### Cenário: Atualizar Status de Pedidos

**1. Query Principal:**
```sql
SELECT ID, CLIENTE, STATUS FROM PEDIDOS
```

**2. DBAction (ID: 456):**
```sql
UPDATE PEDIDOS
SET STATUS = :VAR_STATUS_NOVO
WHERE ID = :VAR_ROW_ID;
```

**3. Configuração no ComponentData:**
```json
{
  "title": "Pedidos",
  "query": "SELECT ID, CLIENTE, STATUS FROM PEDIDOS",
  "columns": "[{\"columnName\":\"ID\",\"dataField\":\"ID\",\"columnType\":\"data_number\",\"width\":\"80px\"},{\"columnName\":\"CLIENTE\",\"dataField\":\"CLIENTE\",\"columnType\":\"data_text\"},{\"columnName\":\"STATUS\",\"dataField\":\"STATUS\",\"columnType\":\"input_dropdown\",\"dropdownOptionsQuery\":\"SELECT ID, NOME FROM STATUS\",\"onChangeAction\":\"dbaction:456\",\"valueVariable\":\":VAR_STATUS_NOVO\"}]"
}
```

**4. O que acontece quando o usuário altera o dropdown:**
1. Usuário seleciona novo status no dropdown
2. Sistema define `:VAR_ROW_ID` = ID do pedido (ex: `5`)
3. Sistema define `:VAR_STATUS_NOVO` = novo status selecionado (ex: `"ATIVO"`)
4. Sistema executa DBAction 456
5. DBAction faz: `UPDATE PEDIDOS SET STATUS = 'ATIVO' WHERE ID = 5`
6. Tabela atualiza visualmente

---

## Outros Tipos de Coluna Editável

### Input Text
```json
{
  "columnName": "NOME",
  "dataField": "NOME",
  "columnType": "input_text",
  "onChangeAction": "dbaction:123",
  "valueVariable": ":VAR_NOME_NOVO"
}
```

### Input Number
```json
{
  "columnName": "VALOR",
  "dataField": "VALOR",
  "columnType": "input_number",
  "onChangeAction": "dbaction:123",
  "valueVariable": ":VAR_VALOR_NOVO"
}
```

### Checkbox
```json
{
  "columnName": "ATIVO",
  "dataField": "ATIVO",
  "columnType": "data_boolean_checkbox",
  "onChangeAction": "dbaction:123",
  "valueVariable": ":VAR_ATIVO_NOVO"
}
```
**Nota:** Checkbox envia `1` (marcado) ou `0` (desmarcado)

### Input Date
```json
{
  "columnName": "DATA",
  "dataField": "DATA",
  "columnType": "input_date",
  "onChangeAction": "dbaction:123",
  "valueVariable": ":VAR_DATA_NOVA"
}
```

---

## Checklist de Configuração

Antes de testar, verifique:

- [ ] DBAction criada no Mitra com ID anotado
- [ ] DBAction usa `:VAR_ROW_ID` (ou variável configurada em `rowIdVariable`)
- [ ] DBAction usa a variável definida em `valueVariable` (ex: `:VAR_STATUS_NOVO`)
- [ ] Coluna tem `columnType: "input_dropdown"` (ou outro tipo editável)
- [ ] Coluna tem `onChangeAction: "dbaction:ID"` (onde ID é o ID da DBAction)
- [ ] Coluna tem `valueVariable: ":VAR_NOME_DA_VARIAVEL"` (começa com `:VAR_`)
- [ ] `dropdownOptionsQuery` retorna pelo menos 2 colunas (ID e Nome/Label)

---

## Troubleshooting

### Problema: Dropdown não salva no banco

**Verifique:**
1. ✅ `valueVariable` está configurada na coluna?
2. ✅ Nome da variável na DBAction é igual ao `valueVariable`?
3. ✅ DBAction está usando `:VAR_ROW_ID` (ou variável configurada)?
4. ✅ ID da DBAction em `onChangeAction` está correto?

**Exemplo de erro comum:**
```json
// ❌ ERRADO - variável não começa com :VAR_
"valueVariable": "STATUS_NOVO"

// ✅ CORRETO
"valueVariable": ":VAR_STATUS_NOVO"
```

### Problema: DBAction executa mas não atualiza

**Verifique:**
1. ✅ SQL da DBAction está correto?
2. ✅ Nome da coluna no UPDATE é igual ao campo no banco?
3. ✅ Tipo de dado está correto (string, número, etc.)?

**Exemplo:**
```sql
-- Se STATUS é VARCHAR no banco:
SET STATUS = :VAR_STATUS_NOVO  ✅

-- Se STATUS é INT no banco e você está enviando string:
SET STATUS = CAST(:VAR_STATUS_NOVO AS INT)  ✅
```

### Problema: Dropdown não mostra opções

**Verifique:**
1. ✅ `dropdownOptionsQuery` está retornando dados?
2. ✅ Query retorna pelo menos 2 colunas?
3. ✅ Primeira coluna é o `value` (ID) e segunda é o `label` (Nome)?

**Exemplo de query correta:**
```sql
-- ✅ CORRETO - retorna ID e NOME
SELECT ID, NOME FROM STATUS

-- ❌ ERRADO - retorna só uma coluna
SELECT NOME FROM STATUS
```

---

## Ordem de Execução (Importante)

O sistema executa nesta ordem **automaticamente**:

1. **Define `:VAR_ROW_ID`** = ID da linha sendo editada
2. **Define `valueVariable`** (ex: `:VAR_STATUS_NOVO`) = novo valor selecionado
3. **Executa DBAction** = SQL roda com ambas variáveis disponíveis

**Você não precisa fazer nada além de configurar!** A ordem é garantida pelo código.

---

## Dicas Importantes

1. **Nomes de Variáveis:**
   - Sempre começam com `:VAR_`
   - Use nomes descritivos: `:VAR_STATUS_NOVO`, `:VAR_NOME_NOVO`, etc.
   - Evite espaços ou caracteres especiais

2. **Múltiplas Colunas Editáveis:**
   - Cada coluna pode ter sua própria `valueVariable`
   - Não há conflito entre elas
   - Exemplo: uma coluna usa `:VAR_STATUS`, outra usa `:VAR_PRIORIDADE`

3. **Compatibilidade:**
   - Se você **não** definir `valueVariable`, o comportamento antigo é mantido
   - Código existente continua funcionando
   - Apenas adicione `valueVariable` quando precisar salvar no backend

4. **Performance:**
   - Impacto mínimo (1 chamada extra de `setVariableMitra`)
   - Não afeta scroll ou renderização
   - Funciona perfeitamente com 2000+ linhas

---

## Exemplo Real Completo

**Cenário:** Tabela de clientes com campo "Tipo" editável via dropdown

**1. Query:**
```sql
SELECT ID, NOME, EMAIL, TIPO FROM CLIENTES
```

**2. Query de Opções do Dropdown:**
```sql
SELECT ID, DESCRICAO FROM TIPOS_CLIENTE
```

**3. DBAction (ID: 789):**
```sql
UPDATE CLIENTES
SET TIPO = :VAR_TIPO_NOVO
WHERE ID = :VAR_ROW_ID;
```

**4. ComponentData completo:**
```json
{
  "title": "Clientes",
  "subtitle": "Gerencie os clientes do sistema",
  "query": "SELECT ID, NOME, EMAIL, TIPO FROM CLIENTES",
  "rowIdVariable": ":VAR_CLIENTE_ID",
  "columns": "[{\"columnName\":\"ID\",\"dataField\":\"ID\",\"columnType\":\"data_number\",\"width\":\"80px\",\"sortable\":true},{\"columnName\":\"NOME\",\"dataField\":\"NOME\",\"columnType\":\"data_text\",\"width\":\"200px\"},{\"columnName\":\"EMAIL\",\"dataField\":\"EMAIL\",\"columnType\":\"data_text\",\"width\":\"250px\"},{\"columnName\":\"TIPO\",\"dataField\":\"TIPO\",\"columnType\":\"input_dropdown\",\"width\":\"150px\",\"dropdownOptionsQuery\":\"SELECT ID, DESCRICAO FROM TIPOS_CLIENTE\",\"onChangeAction\":\"dbaction:789\",\"valueVariable\":\":VAR_TIPO_NOVO\"}]"
}
```

**5. O que acontece:**
- Usuário vê lista de clientes
- Clica no dropdown "TIPO" de um cliente
- Seleciona novo tipo (ex: "VIP")
- Sistema define `:VAR_CLIENTE_ID = 10` e `:VAR_TIPO_NOVO = "VIP"`
- DBAction 789 executa: `UPDATE CLIENTES SET TIPO = 'VIP' WHERE ID = 10`
- Tabela atualiza mostrando novo tipo

---

## Resumo Rápido

**Para fazer dropdown salvar no backend:**

1. ✅ Crie DBAction com `:VAR_ROW_ID` e sua variável custom (ex: `:VAR_STATUS_NOVO`)
2. ✅ Adicione `onChangeAction: "dbaction:ID"` na coluna
3. ✅ Adicione `valueVariable: ":VAR_STATUS_NOVO"` na coluna (mesmo nome da DBAction)
4. ✅ Pronto! Agora salva automaticamente

**Lembre-se:** A variável na DBAction deve ter o **mesmo nome** do `valueVariable` na coluna!
