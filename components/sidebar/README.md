# Sidebar - Componente de Navegação

## Descrição

Componente de sidebar moderna e completa para navegação em aplicações Mitra. Oferece funcionalidades de navegação colapsável, busca, badges, perfil de usuário, submenus expansíveis e integração completa com as funções nativas da plataforma Mitra.

**Compatibilidade:** Totalmente compatível com o componente Menu nativo do Mitra, suportando configuração via queries SQL (`menuQuery`, `userQuery`) ou formato JSON tradicional (`menuItems`, `userInfo`).

**Características principais:**

- Sidebar colapsável (expandida/colapsada)
- Campo de busca para filtrar itens do menu
- Suporte a badges/contadores nos itens
- Seção de perfil do usuário com avatar e informações
- Menu dropdown de ações do perfil (userMenu)
- Submenus expansíveis com hierarquia
- Agrupamento de itens por seções
- Tooltips no estado colapsado
- Integração completa com funções Mitra (modalMitra, actionMitra, setVariableMitra, queryMitra)
- Carregamento de telas Mitra em iframe interno (substitui conteúdo principal)
- Suporte a URLs embedded do formato Mitra (navegação em iframe)
- Botão de filtro flutuante (FAB) com modal lateral deslizante
- Sistema de filtros dinâmico por tela
- Design responsivo com tema claro
- Scrollbar customizada

## Configuração

### Variáveis do componentData

#### Configuração do Cabeçalho

| Variável | Tipo | Obrigatório | Descrição | Exemplo |
|----------|------|-------------|-----------|---------|
| `headerLogo` | string | Não | URL da imagem do logo ou "DEFAULTLOGO" | `"DEFAULTLOGO"` ou `"https://..."` |
| `headerTitle` | string | Não | Texto principal ao lado do logo | `"Meu App"` |
| `headerSubtitle` | string | Não | Texto secundário ao lado do logo | `"Gestão de Vendas"` |
| `logo` | string | Não | Nome da aplicação (compatibilidade) | `"Minha App"` |
| `logoIcon` | string | Não | Texto/letra exibida no ícone do logo | `"M"` |

#### Configuração do Menu

##### Opção 1: Via Query SQL (Padrão do Menu Nativo)

| Variável | Tipo | Obrigatório | Descrição | Exemplo |
|----------|------|-------------|-----------|---------|
| `menuQuery` | string (SQL) | Sim* | Query SQL que retorna os itens de navegação | Ver exemplo abaixo |
| `menuIdColumn` | string | Sim* | Nome da coluna com ID único do item | `"ID"` |
| `menuNameColumn` | string | Sim* | Nome da coluna com texto do item | `"NAME"` |
| `menuIconColumn` | string | Sim* | Nome da coluna com ícone Lucide (PascalCase) | `"ICON"` |
| `menuGroupColumn` | string | Não | Nome da coluna que define a seção do menu | `"GRUPO"` |
| `menuParentIdColumn` | string | Não | Nome da coluna com ID do item-pai (submenus) | `"PARENTID"` |
| `menuUrlColumn` | string | Sim* | Nome da coluna com URL da tela (formato embedded) | `"MENUURL"` |

##### Opção 2: Via JSON (Compatibilidade)

| Variável | Tipo | Obrigatório | Descrição | Exemplo |
|----------|------|-------------|-----------|---------|
| `menuItems` | string (JSON) | Sim* | Array de objetos com os itens do menu | Ver exemplo abaixo |

\* Use `menuQuery` OU `menuItems` (não ambos). Se `menuQuery` estiver configurado, ele tem prioridade.

#### Configuração do Usuário

##### Opção 1: Via Query SQL para Dados do Usuário (Padrão do Menu Nativo)

| Variável | Tipo | Obrigatório | Descrição | Exemplo |
|----------|------|-------------|-----------|---------|
| `userQuery` | string (SQL) | Não | Query SQL que retorna dados do usuário logado | `"SELECT NOME, EMAIL, FOTO_URL FROM USUARIOS WHERE ID = :VAR_USER"` |
| `userName` | string | Não | Nome da coluna com nome do usuário | `"NOME"` |
| `userEmail` | string | Não | Nome da coluna com e-mail do usuário | `"EMAIL"` |
| `userAvatar` | string | Não | Nome da coluna com URL da foto do usuário | `"FOTO_URL"` |

##### Opção 2: Via JSON para Dados do Usuário (Compatibilidade)

| Variável | Tipo | Obrigatório | Descrição | Exemplo |
|----------|------|-------------|-----------|---------|
| `userInfo` | string (JSON) | Não | Informações do usuário (nome, email, avatar) | Ver exemplo abaixo |

#### Menu de Ações do Perfil

| Variável | Tipo | Obrigatório | Descrição | Exemplo |
|----------|------|-------------|-----------|---------|
| `userMenu` | string (JSON) | Não | Array de ações no dropdown do perfil | Ver exemplo abaixo |

#### Sistema de Filtros por Tela

| Variável | Tipo | Obrigatório | Descrição | Exemplo |
|----------|------|-------------|-----------|---------|
| `filtersByScreen` | string (JSON) | Não | Mapeamento de screenId para configuração de filtros | Ver exemplo abaixo |

**Observação:** Todas as variáveis chegam como string. Use `JSON.parse()` para objetos JSON e `parseInt()` para números quando necessário.

#### Outras Configurações

| Variável | Tipo | Obrigatório | Descrição | Exemplo |
|----------|------|-------------|-----------|---------|
| `actionLogoutId` | string | Não | ID da Action do Mitra para logout (compatibilidade) | `"5"` |
| `primaryColor` | string | Não | Cor primária personalizada (hex) | `"#3B82F6"` |
| `activeItemId` | string | Não | ID do item ativo inicial | `"dashboard"` |

### Configuração via menuQuery (SQL) - Padrão do Menu Nativo

A `menuQuery` é uma query SQL que retorna os itens de navegação. **Use esta opção para máxima compatibilidade com o Menu nativo do Mitra.**

#### Fórmula Obrigatória para URLs

A coluna mapeada em `menuUrlColumn` **DEVE** ser construída usando a fórmula SQL abaixo:

```sql
CONCAT(
    :VAR_URL, 
    '/embedded', 
    '?inlineToken=', 
    :VAR_TOKEN, 
    '&inlineScreenId=', 
    (SELECT ID FROM INT_SCREEN WHERE NAME = 'NomeDaSuaTela')
)
```

#### Exemplo de menuQuery

```sql
SELECT
    1 AS ID,
    'Dashboard' AS NAME,
    'LayoutDashboard' AS ICON,
    'Principal' AS GRUPO,
    '' AS PARENTID,
    CONCAT(:VAR_URL, '/embedded', '?inlineToken=', :VAR_TOKEN, '&inlineScreenId=', (SELECT ID FROM INT_SCREEN WHERE NAME = 'Dashboard')) AS MENUURL
FROM DUAL
UNION ALL
SELECT
    2 AS ID,
    'Clientes' AS NAME,
    'Users' AS ICON,
    'Cadastros' AS GRUPO,
    '' AS PARENTID,
    CONCAT(:VAR_URL, '/embedded', '?inlineToken=', :VAR_TOKEN, '&inlineScreenId=', (SELECT ID FROM INT_SCREEN WHERE NAME = 'Clientes')) AS MENUURL
FROM DUAL
```

##### Mapeamento de Colunas

Configure as variáveis de mapeamento conforme os nomes das colunas na sua query:

```json
{
  "menuQuery": "SUA_QUERY_ACIMA",
  "menuIdColumn": "ID",
  "menuNameColumn": "NAME",
  "menuIconColumn": "ICON",
  "menuGroupColumn": "GRUPO",
  "menuParentIdColumn": "PARENTID",
  "menuUrlColumn": "MENUURL"
}
```

#### Regras para Ícones

- **Formato obrigatório:** PascalCase (ex: `Users`, `PieChart`, `LayoutDashboard`)
- **Biblioteca:** Ícones Lucide React
- **Exemplos válidos:** `Home`, `User`, `Settings`, `Bell`, `FileText`, `BarChart3`, `LayoutDashboard`, `Users`, `PieChart`
- O componente converte automaticamente para lowercase para renderização

#### Submenus via menuQuery

Para criar submenus, use `menuParentIdColumn`:

```sql
-- Item pai
SELECT 1 AS ID, 'Relatórios' AS NAME, 'Chart' AS ICON, 'Principal' AS GRUPO, '' AS PARENTID, ... AS MENUURL
FROM DUAL
UNION ALL
-- Subitem
SELECT 2 AS ID, 'Vendas' AS NAME, 'Chart' AS ICON, 'Principal' AS GRUPO, '1' AS PARENTID, ... AS MENUURL
FROM DUAL
```

### Configuração via menuItems (JSON) - Compatibilidade

O campo `menuItems` deve ser um JSON string com um array de objetos. Cada item pode ter:

```json
{
  "id": "string",              // ID único do item (obrigatório)
  "label": "string",           // Texto exibido (obrigatório)
  "icon": "string",           // Nome do ícone (obrigatório): home, dashboard, user, settings, bell, file, chart
  "screenId": "string",       // ID da tela para abrir (opcional - usado com modalMitra ou em Actions)
  "actionId": "string",       // ID da Action para executar via actionMitra - navegação em iframe (opcional)
  "navigationType": "string", // Tipo de navegação: "modal" (padrão) ou "iframe" (opcional)
  "query": "string",          // Query SQL para executar via queryMitra (opcional)
  "badge": "string",          // Badge/contador exibido ao lado do item (opcional)
  "contextVariable": "string", // Nome da variável Mitra para definir antes de navegar (opcional)
  "contextValue": "string",   // Valor da variável de contexto (padrão: usa item.id)
  "modalWidth": "string",     // Largura do modal em % (padrão: 100) - apenas para navigationType="modal"
  "modalHeight": "string",    // Altura do modal em % (padrão: 100) - apenas para navigationType="modal"
  "submenu": [                // Array de subitens (opcional)
    {
      "id": "string",
      "label": "string",
      "screenId": "string",
      "actionId": "string",
      "badge": "string"
    }
  ]
}
```

### Configuração via userQuery (SQL) - Padrão do Menu Nativo

A `userQuery` é uma query SQL que retorna **uma única linha** com os dados do usuário logado.

#### Exemplo de userQuery

```sql
SELECT NOME, EMAIL, FOTO_URL 
FROM USUARIOS 
WHERE ID = :VAR_USER
```

##### Mapeamento de Colunas do Usuário

```json
{
  "userQuery": "SELECT NOME, EMAIL, FOTO_URL FROM USUARIOS WHERE ID = :VAR_USER",
  "userName": "NOME",
  "userEmail": "EMAIL",
  "userAvatar": "FOTO_URL"
}
```

##### Opção 2: Via JSON para Informações do Usuário (Compatibilidade)

```json
{
  "name": "string",      // Nome completo do usuário
  "email": "string",     // E-mail do usuário
  "avatar": "string",    // URL da foto do usuário
  "role": "string",      // Cargo/função do usuário (compatibilidade)
  "initials": "string"   // Iniciais para o avatar (geradas automaticamente se não houver avatar)
}
```

### Configuração do userMenu (Menu de Ações do Perfil)

O `userMenu` é um JSON string que define as ações no dropdown do perfil. **Deve conter pelo menos uma ação de "Sair".**

#### Estrutura do userMenu

```json
[
  {
    "nome": "string",           // Nome da ação (obrigatório)
    "icone": "string",          // Nome do ícone Lucide em PascalCase (obrigatório)
    "tipoInteracao": "string",  // Tipo: "form", "action" ou "modal" (obrigatório)
    "idInteracao": number       // ID da ação (obrigatório)
  }
]
```

#### Tipos de Interação

- `"form"`: Abre um formulário
- `"action"`: Executa uma Action do Mitra
- `"modal"`: Abre uma tela em modal

#### Exemplo de userMenu

```json
[
  {
    "nome": "Minha Conta",
    "icone": "User",
    "tipoInteracao": "form",
    "idInteracao": 1
  },
  {
    "nome": "Sair",
    "icone": "LogOut",
    "tipoInteracao": "action",
    "idInteracao": 99
  }
]
```

**No componentData (como string):**

```json
{
  "userMenu": "[{\\\"nome\\\":\\\"Minha Conta\\\",\\\"icone\\\":\\\"User\\\",\\\"tipoInteracao\\\":\\\"form\\\",\\\"idInteracao\\\":1},{\\\"nome\\\":\\\"Sair\\\",\\\"icone\\\":\\\"LogOut\\\",\\\"tipoInteracao\\\":\\\"action\\\",\\\"idInteracao\\\":99}]"
}
```

### Configuração do Sistema de Filtros por Tela

O componente inclui um botão flutuante (FAB) no canto inferior direito que abre um modal lateral com filtros customizáveis. Cada tela pode ter sua própria configuração de filtros.

#### Estrutura de `filtersByScreen`

O campo `filtersByScreen` é um JSON que pode ser configurado em dois formatos:

**Formato Recomendado (Array de Objetos):**

```json
{
  "filtersByScreen": "[{\"id\":\"10\",\"title\":\"Filtros de Clientes\",\"fields\":[{\"type\":\"text\",\"id\":\"nome\",\"label\":\"Nome\",\"placeholder\":\"Buscar por nome...\",\"variable\":\":VAR_FILTER_NOME\"},{\"type\":\"select\",\"id\":\"status\",\"label\":\"Status\",\"options\":[{\"value\":\"\",\"label\":\"Todos\"},{\"value\":\"ativo\",\"label\":\"Ativo\"},{\"value\":\"inativo\",\"label\":\"Inativo\"}],\"variable\":\":VAR_FILTER_STATUS\"},{\"type\":\"dateRange\",\"id\":\"periodo\",\"label\":\"Período\",\"variableStart\":\":VAR_DATA_INICIO\",\"variableEnd\":\":VAR_DATA_FIM\"},{\"type\":\"checkbox\",\"id\":\"categorias\",\"label\":\"Categorias\",\"options\":[{\"value\":\"1\",\"label\":\"Categoria A\"},{\"value\":\"2\",\"label\":\"Categoria B\"}],\"variable\":\":VAR_FILTER_CATEGORIAS\"}]},{\"id\":\"15\",\"title\":\"Filtros de Pedidos\",\"fields\":[{\"type\":\"text\",\"id\":\"codigo\",\"label\":\"Código\",\"placeholder\":\"Buscar código...\",\"variable\":\":VAR_FILTER_CODIGO\"}]}]"
}
```

**Formato Alternativo (Objeto com IDs como Chaves) - Compatibilidade:**

```json
{
  "filtersByScreen": "{\"10\":{\"title\":\"Filtros de Clientes\",\"fields\":[...]},\"15\":{\"title\":\"Filtros de Pedidos\",\"fields\":[...]}}"
}
```

**Estrutura de cada objeto de filtro:**

- `id` (obrigatório): ID da tela (string ou número)
- `title` (opcional): Título exibido no header do modal de filtros
- `fields` (obrigatório): Array de campos de filtro

#### Tipos de Campos Suportados

| Tipo | Descrição | Propriedades | Formato da Variável |
|------|-----------|--------------|---------------------|
| `text` | Campo de texto livre | `id`, `label`, `placeholder`, `variable` | String digitada |
| `select` | Dropdown seleção única | `id`, `label`, `options` (array de `{value, label}`) **ou** `optionsQuery`, `optionsValueColumn`, `optionsLabelColumn`, `variable` | Valor selecionado |
| `multiselect` | Dropdown múltipla seleção com busca | `id`, `label`, `placeholder` (opcional), `options` (array de `{value, label}`) **ou** `optionsQuery`, `optionsValueColumn`, `optionsLabelColumn`, `variable` | Valores separados por vírgula |
| `checkbox` | Múltipla seleção | `id`, `label`, `options` (array de `{value, label}`), `variable` | Valores separados por vírgula |
| `dateRange` | Período (início/fim) | `id`, `label`, `variableStart`, `variableEnd` | Duas variáveis: início e fim (formato YYYY-MM-DD) |
| `year` | Dropdown de ano | `id`, `label`, `options` (array de `{value, label}`) **ou** `optionsQuery`, `optionsValueColumn`, `optionsLabelColumn`, `variable` | Valor do ano selecionado (ex: "2024") |
| `month` | Dropdown de mês | `id`, `label`, `variable` | Valor numérico do mês (01 a 12) |

**Nota sobre campos `select` e `multiselect`:** Ambos os tipos suportam duas formas de definir opções:

1. **Opções Estáticas:** Use a propriedade `options` com um array de objetos `{value, label}`
2. **Opções Dinâmicas via Query:** Use `optionsQuery` para carregar opções do banco de dados dinamicamente

**Diferença entre `multiselect` e `checkbox`:**
- **`multiselect`**: Ideal para listas longas de opções. Oferece campo de busca integrado, tags visuais para itens selecionados e interface mais compacta. O placeholder do campo de busca mostra informações sobre as seleções (nome da opção se apenas uma selecionada, ou "X selecionadas" se múltiplas).
- **`checkbox`**: Ideal para listas curtas onde todas as opções devem estar visíveis simultaneamente.

Veja exemplos detalhados abaixo.

#### Exemplo Completo de Configuração

**Formato Recomendado (Array):**

```json
{
  "filtersByScreen": "[{\"id\":\"10\",\"title\":\"Filtros de Clientes\",\"fields\":[{\"type\":\"text\",\"id\":\"nome\",\"label\":\"Nome\",\"placeholder\":\"Buscar por nome...\",\"variable\":\":VAR_FILTER_NOME\"},{\"type\":\"select\",\"id\":\"status\",\"label\":\"Status\",\"options\":[{\"value\":\"\",\"label\":\"Todos\"},{\"value\":\"ativo\",\"label\":\"Ativo\"},{\"value\":\"inativo\",\"label\":\"Inativo\"}],\"variable\":\":VAR_FILTER_STATUS\"},{\"type\":\"multiselect\",\"id\":\"categorias\",\"label\":\"Categorias\",\"placeholder\":\"Buscar categorias...\",\"variable\":\":VAR_CATEGORIAS\",\"optionsQuery\":\"SELECT ID, NOME FROM CATEGORIAS ORDER BY NOME\",\"optionsValueColumn\":\"ID\",\"optionsLabelColumn\":\"NOME\"},{\"type\":\"dateRange\",\"id\":\"periodo\",\"label\":\"Período\",\"variableStart\":\":VAR_DATA_INICIO\",\"variableEnd\":\":VAR_DATA_FIM\"},{\"type\":\"checkbox\",\"id\":\"tags\",\"label\":\"Tags\",\"options\":[{\"value\":\"vip\",\"label\":\"VIP\"},{\"value\":\"premium\",\"label\":\"Premium\"}],\"variable\":\":VAR_FILTER_TAGS\"}]},{\"id\":\"15\",\"title\":\"Filtros de Pedidos\",\"fields\":[{\"type\":\"text\",\"id\":\"codigo\",\"label\":\"Código\",\"placeholder\":\"Buscar código...\",\"variable\":\":VAR_FILTER_CODIGO\"}]}]"
}
```

**Vantagens do formato array:**

- Mais legível e organizado
- Consistente com outras estruturas do componente
- Facilita adicionar/remover filtros
- Cada configuração tem seu próprio campo `id` explícito

#### Select com Opções Dinâmicas via Query

Para campos `select`, você pode carregar opções dinamicamente do banco de dados usando uma query SQL. Isso é útil quando as opções mudam frequentemente ou são muitas para definir manualmente.

**Exemplo com Query Dinâmica:**

```json
{
  "type": "select",
  "id": "centro_resultado",
  "label": "Centro de Resultado",
  "variable": ":VAR_FILTRO_CENTRO",
  "optionsQuery": "SELECT ID, DESCR FROM CENTRO_RESULTADO",
  "optionsValueColumn": "ID",
  "optionsLabelColumn": "DESCR"
}
```

**Propriedades para Query Dinâmica:**

- `optionsQuery` (obrigatório): Query SQL que retorna as opções. Deve retornar pelo menos 2 colunas.
- `optionsValueColumn` (opcional): Nome da coluna que será usada como valor da opção. Se não informado, usa a primeira coluna do resultado.
- `optionsLabelColumn` (opcional): Nome da coluna que será usada como label da opção. Se não informado, usa a segunda coluna do resultado.

**Como Funciona:**

1. Quando o modal de filtros é aberto, o sistema executa a query definida em `optionsQuery` usando `queryMitra()`
2. O resultado é mapeado para o formato `{value, label}` usando as colunas especificadas
3. As opções são renderizadas no dropdown
4. Se a query falhar ou não retornar dados, o dropdown ficará vazio (com apenas a opção "Selecione...")

**Exemplo Completo com Select Dinâmico:**

```json
{
  "filtersByScreen": "[{\"id\":\"10\",\"title\":\"Filtros de Análise\",\"fields\":[{\"type\":\"select\",\"id\":\"centro_resultado\",\"label\":\"Centro de Resultado\",\"variable\":\":VAR_FILTRO_CENTRO\",\"optionsQuery\":\"SELECT ID, DESCR FROM CENTRO_RESULTADO\",\"optionsValueColumn\":\"ID\",\"optionsLabelColumn\":\"DESCR\"},{\"type\":\"select\",\"id\":\"status\",\"label\":\"Status\",\"options\":[{\"value\":\"ativo\",\"label\":\"Ativo\"},{\"value\":\"inativo\",\"label\":\"Inativo\"}],\"variable\":\":VAR_FILTER_STATUS\"}]}]"
}
```

**Observações Importantes:**

- A query é executada toda vez que o modal de filtros é aberto
- Use queries simples e rápidas para melhor performance
- A primeira linha do resultado pode ser tratada como cabeçalho se contiver strings em maiúsculas
- Se não especificar `optionsValueColumn` ou `optionsLabelColumn`, o sistema usa a primeira e segunda colunas respectivamente

#### Filtros de Ano e Mês

**Exemplo com Ano via Query e Mês Estático:**

```json
{
  "filtersByScreen": "[{\"id\":\"10\",\"title\":\"Filtros por Período\",\"fields\":[{\"type\":\"year\",\"id\":\"ano\",\"label\":\"Ano\",\"variable\":\":VAR_FILTRO_ANO\",\"optionsQuery\":\"SELECT DISTINCT YEAR(DATA_CADASTRO) AS ANO FROM PEDIDOS ORDER BY ANO DESC\",\"optionsValueColumn\":\"ANO\",\"optionsLabelColumn\":\"ANO\"},{\"type\":\"month\",\"id\":\"mes\",\"label\":\"Mês\",\"variable\":\":VAR_FILTRO_MES\"}]}]"
}
```

**Propriedades do Filtro Year:**

- `type`: "year" (obrigatório)
- `id`: Identificador único do campo (obrigatório)
- `label`: Texto do label (obrigatório)
- `variable`: Nome da variável Mitra (obrigatório, deve começar com `:VAR_`)
- `optionsQuery`: Query SQL para carregar anos disponíveis (opcional)
- `optionsValueColumn`: Nome da coluna que será usada como valor (opcional)
- `optionsLabelColumn`: Nome da coluna que será usada como label (opcional)
- `options`: Array de opções estáticas `{value, label}` (opcional, alternativa ao optionsQuery)

**Propriedades do Filtro Month:**

- `type`: "month" (obrigatório)
- `id`: Identificador único do campo (obrigatório)
- `label`: Texto do label (obrigatório)
- `variable`: Nome da variável Mitra (obrigatório, deve começar com `:VAR_`)

**Observações:**

- O filtro de mês sempre retorna valores de "01" a "12" (com zero à esquerda)
- O filtro de ano pode ter opções estáticas ou carregadas via query
- Ambos os filtros funcionam de forma independente
- Os valores são salvos em variáveis Mitra separadas

**Exemplo Completo de Configuração com Ano e Mês:**

```json
{
  "filtersByScreen": "[{\"id\":\"47\",\"title\":\"Filtros de Análise\",\"fields\":[{\"type\":\"year\",\"id\":\"ano_analise\",\"label\":\"Ano de Análise\",\"variable\":\":VAR_FILTRO_ANO\",\"optionsQuery\":\"SELECT DISTINCT YEAR(DATA_CADASTRO) AS ANO FROM PEDIDOS WHERE YEAR(DATA_CADASTRO) >= 2020 ORDER BY ANO DESC\",\"optionsValueColumn\":\"ANO\",\"optionsLabelColumn\":\"ANO\"},{\"type\":\"month\",\"id\":\"mes_analise\",\"label\":\"Mês de Análise\",\"variable\":\":VAR_FILTRO_MES\"},{\"type\":\"select\",\"id\":\"status\",\"label\":\"Status\",\"options\":[{\"value\":\"ativo\",\"label\":\"Ativo\"},{\"value\":\"inativo\",\"label\":\"Inativo\"}],\"variable\":\":VAR_FILTER_STATUS\"}]}]"
}
```

#### Multiselect - Múltipla Seleção com Busca

O campo `multiselect` é um dropdown customizado que permite múltiplas seleções com funcionalidade de busca integrada. É ideal para listas longas de opções onde o usuário precisa filtrar e selecionar múltiplos itens.

**Características Especiais:**

- **Campo de busca integrado**: Filtra opções em tempo real conforme o usuário digita
- **Tags visuais**: Mostra itens selecionados como badges abaixo do campo, com botão para remover individualmente
- **Placeholder inteligente**: 
  - Quando nenhum item está selecionado: mostra o `placeholder` configurado (ou "Buscar e selecionar..." por padrão)
  - Quando 1 item está selecionado: mostra o nome da opção selecionada
  - Quando múltiplos itens estão selecionados: mostra "X selecionadas" (onde X é a quantidade)
- **Dropdown customizado**: Não usa `<select>` nativo, oferecendo melhor controle visual e funcionalidades avançadas
- **Performance otimizada**: Filtragem eficiente mesmo com centenas de opções

**Exemplo com Opções Estáticas:**

```json
{
  "type": "multiselect",
  "id": "categorias",
  "label": "Categorias",
  "placeholder": "Buscar categorias...",
  "variable": ":VAR_CATEGORIAS",
  "options": [
    {"value": "1", "label": "Categoria A"},
    {"value": "2", "label": "Categoria B"},
    {"value": "3", "label": "Categoria C"}
  ]
}
```

**Exemplo com Query Dinâmica:**

```json
{
  "type": "multiselect",
  "id": "status",
  "label": "Status",
  "placeholder": "Buscar status...",
  "variable": ":VAR_STATUS",
  "optionsQuery": "SELECT ID, NOME FROM STATUS ORDER BY NOME",
  "optionsValueColumn": "ID",
  "optionsLabelColumn": "NOME"
}
```

**Formato da Variável:**

- Valores são salvos separados por vírgula: `"1,2,3"`
- Variável vazia quando nenhum item selecionado: `""`
- Mesmo formato do campo `checkbox` para compatibilidade com queries SQL existentes

**Uso em Queries SQL:**

O formato de valores separados por vírgula permite usar a mesma lógica de conversão do campo `checkbox`:

```sql
-- Converter valores separados por vírgula em lista para IN
SELECT * FROM PRODUTOS 
WHERE CATEGORIA_ID IN (
    SELECT TO_NUMBER(REGEXP_SUBSTR(:VAR_CATEGORIAS, '[^,]+', 1, LEVEL))
    FROM DUAL
    CONNECT BY REGEXP_SUBSTR(:VAR_CATEGORIAS, '[^,]+', 1, LEVEL) IS NOT NULL
)
```

**Exemplo Completo com Multiselect:**

```json
{
  "filtersByScreen": "[{\"id\":\"10\",\"title\":\"Filtros de Produtos\",\"fields\":[{\"type\":\"text\",\"id\":\"nome\",\"label\":\"Nome\",\"placeholder\":\"Buscar por nome...\",\"variable\":\":VAR_FILTER_NOME\"},{\"type\":\"multiselect\",\"id\":\"categorias\",\"label\":\"Categorias\",\"placeholder\":\"Buscar categorias...\",\"variable\":\":VAR_CATEGORIAS\",\"optionsQuery\":\"SELECT ID, NOME FROM CATEGORIAS ORDER BY NOME\",\"optionsValueColumn\":\"ID\",\"optionsLabelColumn\":\"NOME\"},{\"type\":\"multiselect\",\"id\":\"status\",\"label\":\"Status\",\"variable\":\":VAR_STATUS\",\"options\":[{\"value\":\"ativo\",\"label\":\"Ativo\"},{\"value\":\"inativo\",\"label\":\"Inativo\"},{\"value\":\"pendente\",\"label\":\"Pendente\"}]}]}]"
}
```

**Quando Usar Multiselect vs Checkbox:**

- **Use `multiselect` quando:**
  - Há muitas opções (mais de 5-7 itens)
  - As opções podem mudar dinamicamente (via query)
  - Você quer economizar espaço vertical na interface
  - A busca é importante para o usuário encontrar opções rapidamente

- **Use `checkbox` quando:**
  - Há poucas opções (até 5-7 itens)
  - Todas as opções devem estar sempre visíveis
  - Você quer que o usuário veja todas as opções de uma vez

#### Como Configurar filtersByScreen Usando o Gerador de Filtros

O componente inclui um gerador visual de filtros (`filter-generator.html`) que facilita a criação dos campos de filtro. Siga os passos abaixo:

##### Passo 1: Gerar os Campos de Filtro

1. Abra o arquivo `filter-generator.html` no navegador
2. Configure os campos de filtro desejados:
   - Selecione ou digite o ID da tela
   - Defina o título do modal (opcional)
   - Adicione os campos de filtro (text, select, multiselect, checkbox, dateRange)
   - Preencha as propriedades de cada campo (id, label, variáveis, opções, etc.)
3. O JSON gerado aparecerá automaticamente no painel lateral
4. Clique em "Copiar para Clipboard" para copiar o array de fields

**Exemplo do JSON gerado (apenas fields):**

```json
[
  {
    "type": "dateRange",
    "id": "periodo",
    "label": "Período de Análise",
    "variableStart": ":VAR_FILTRO_DT_INICIO",
    "variableEnd": ":VAR_FILTRO_DT_FIM"
  },
  {
    "type": "text",
    "id": "nome",
    "label": "Nome",
    "placeholder": "Buscar por nome...",
    "variable": ":VAR_FILTER_NOME"
  }
]
```

##### Passo 2: Configurar na Interface "Configurar JSON"

Na interface "Configurar JSON" do Mitra (mostrada na screenshot), siga estes passos:

1. Clique em "Nova linha" (botão com ícone `+`)
2. Preencha os campos da tabela:
   - **`id`**: ID da tela (ex: `47`, `46`)
   - **`title`**: Título que aparecerá no header do modal de filtros (ex: `"Modal de Filtros"`, `"MODAL DO MENSAL"`)
   - **`fields`**: Cole o JSON copiado do gerador (o array de fields completo)
3. Repita o processo para cada tela que precisa de filtros
4. Clique em "Salvar" para salvar a configuração

##### Estrutura Final do filtersByScreen

Após salvar na interface "Configurar JSON", o formato final do `filtersByScreen` será um objeto onde a chave é o ID da tela:

```json
{
  "47": {
    "title": "Modal de Filtros",
    "fields": [
      {
        "type": "dateRange",
        "id": "periodo",
        "label": "Período de Análise",
        "variableStart": ":VAR_FILTRO_DT_INICIO",
        "variableEnd": ":VAR_FILTRO_DT_FIM"
      }
    ]
  },
  "46": {
    "title": "MODAL DO MENSAL",
    "fields": [
      {
        "type": "text",
        "id": "nome",
        "label": "Nome",
        "placeholder": "Buscar por nome...",
        "variable": ":VAR_FILTER_NOME"
      }
    ]
  }
}
```

##### Observações Importantes

- O gerador retorna apenas o array de `fields` - você precisa adicionar o `id` da tela e o `title` na interface "Configurar JSON"
- Cada linha na tabela representa uma configuração de filtros para uma tela específica
- O `id` na tabela corresponde ao ID da tela (screenId) que será aberta no iframe
- O `title` é opcional, mas recomendado para melhor UX
- O campo `fields` deve conter o JSON completo copiado do gerador

#### Comportamento do Sistema de Filtros

1. **Detecção Automática:** Quando o usuário navega para uma tela, o sistema verifica se existe configuração de filtros para o `screenId` atual
2. **Visibilidade do FAB:** O botão flutuante aparece automaticamente apenas se a tela atual tiver filtros configurados
3. **Badge de Contador:** O FAB exibe um badge com a quantidade de filtros ativos
4. **Persistência:** Os valores dos filtros são salvos em variáveis globais Mitra e persistem entre navegações
5. **Aplicação:** Ao clicar em "Aplicar Filtros", as variáveis são definidas e a tela atual é recarregada automaticamente
6. **Recuperação Automática:** Ao abrir o modal de filtros, os valores previamente salvos nas variáveis `:VAR_` são automaticamente recuperados e os campos são pré-preenchidos

#### Recuperação Automática de Filtros Salvos

O componente implementa recuperação automática de valores de filtros que foram previamente salvos em variáveis globais Mitra (`:VAR_`). Isso permite que os filtros persistam mesmo após recarregar a página (F5) ou navegar para outras telas.

**Como Funciona:**

1. **Quando o modal de filtros é aberto:**
   - O sistema renderiza inicialmente os campos vazios
   - Em seguida, busca automaticamente os valores salvos de cada variável `:VAR_` configurada nos campos de filtro
   - Os valores recuperados são populados no objeto `filterValues` em memória
   - Os campos são re-renderizados com os valores carregados
   - O badge de contador é atualizado para refletir a quantidade de filtros ativos

2. **Busca de Valores:**
   - Para cada campo configurado em `filtersByScreen`, o sistema identifica a variável associada (`field.variable`, `field.variableStart`, `field.variableEnd`)
   - Executa uma query SQL usando `queryMitra()` no formato: `SELECT :VAR_NOME_VARIAVEL as value FROM dual`
   - Se a variável existir e tiver valor, o campo é pré-preenchido
   - Se a variável não existir, estiver vazia ou retornar NULL, o campo permanece vazio

3. **Tratamento de Tipos de Campos:**
   - **Campos simples** (`text`, `select`, `year`, `month`): Busca uma única variável (`field.variable`)
   - **Campos de período** (`dateRange`): Busca duas variáveis separadas (`field.variableStart` e `field.variableEnd`)
   - **Campos de múltipla seleção** (`checkbox`, `multiselect`): Busca uma variável que contém valores separados por vírgula (ex: `"1,2,3"`)

4. **Tratamento de Erros:**
   - Se houver erro ao buscar uma variável específica, o sistema ignora silenciosamente e continua processando as outras variáveis
   - Isso garante que um erro em um campo não impeça o carregamento dos demais filtros

**Exemplo de Fluxo:**

```javascript
// 1. Usuário aplica filtros
// Valores são salvos em variáveis :VAR_FILTER_NOME, :VAR_FILTER_STATUS, etc.

// 2. Usuário recarrega a página (F5)
// Variáveis permanecem salvas no Mitra

// 3. Usuário abre o modal de filtros novamente
// Sistema executa automaticamente:
// - SELECT :VAR_FILTER_NOME as value FROM dual
// - SELECT :VAR_FILTER_STATUS as value FROM dual
// - SELECT :VAR_DATA_INICIO as value FROM dual
// - SELECT :VAR_DATA_FIM as value FROM dual
// Campos são pré-preenchidos com os valores recuperados
```

**Benefícios:**

- **Persistência entre sessões:** Filtros aplicados permanecem mesmo após recarregar a página
- **Experiência do usuário:** Não é necessário reconfigurar filtros toda vez que abrir o modal
- **Consistência:** Os valores salvos são sempre recuperados automaticamente, garantindo que a tela exiba os mesmos dados filtrados
- **Transparência:** O processo é automático e transparente para o usuário

**Observações Importantes:**

- A recuperação acontece **apenas quando o modal de filtros é aberto**, não automaticamente ao carregar a página
- Se uma variável não existir ou estiver vazia, o campo correspondente permanece vazio (comportamento esperado)
- Valores NULL retornados do Mitra são tratados como valores vazios
- Para campos de múltipla seleção, o formato de valores separados por vírgula é mantido (ex: `"1,2,3"`)

#### Uso em Queries SQL

As variáveis definidas pelos filtros podem ser usadas em queries SQL de componentes dentro do iframe:

```sql
SELECT * FROM CLIENTES 
WHERE (:VAR_FILTER_NOME IS NULL OR NOME LIKE '%' || :VAR_FILTER_NOME || '%')
  AND (:VAR_FILTER_STATUS IS NULL OR STATUS = :VAR_FILTER_STATUS)
  AND (:VAR_DATA_INICIO IS NULL OR DATA_CADASTRO >= :VAR_DATA_INICIO)
  AND (:VAR_DATA_FIM IS NULL OR DATA_CADASTRO <= :VAR_DATA_FIM)
  AND (:VAR_FILTER_CATEGORIAS IS NULL OR CATEGORIA_ID IN (
    SELECT TO_NUMBER(REGEXP_SUBSTR(:VAR_FILTER_CATEGORIAS, '[^,]+', 1, LEVEL))
    FROM DUAL
    CONNECT BY REGEXP_SUBSTR(:VAR_FILTER_CATEGORIAS, '[^,]+', 1, LEVEL) IS NOT NULL
  ))
```

**Nota:** Para campos de múltipla seleção (`multiselect` e `checkbox`), os valores são salvos separados por vírgula. Use a query acima como exemplo para converter em lista para uso com `IN`.

**Exemplo com filtros de ano e mês:**

```sql
-- Exemplo com filtros de ano e mês
SELECT * FROM PEDIDOS 
WHERE (:VAR_FILTRO_ANO IS NULL OR YEAR(DATA_PEDIDO) = :VAR_FILTRO_ANO)
  AND (:VAR_FILTRO_MES IS NULL OR LPAD(MONTH(DATA_PEDIDO), 2, '0') = :VAR_FILTRO_MES)
```

### Exemplo de Configuração no Editor Mitra

#### Exemplo 1: Usando menuQuery (Padrão do Menu Nativo)

```json
{
  "headerLogo": "DEFAULTLOGO",
  "headerTitle": "Sistema Mitra",
  "headerSubtitle": "Gestão de Vendas",
  "menuQuery": "SELECT 1 AS ID, 'Dashboard' AS NAME, 'LayoutDashboard' AS ICON, 'Principal' AS GRUPO, '' AS PARENTID, CONCAT(:VAR_URL, '/embedded', '?inlineToken=', :VAR_TOKEN, '&inlineScreenId=', (SELECT ID FROM INT_SCREEN WHERE NAME = 'Dashboard')) AS MENUURL FROM DUAL UNION ALL SELECT 2 AS ID, 'Clientes' AS NAME, 'Users' AS ICON, 'Cadastros' AS GRUPO, '' AS PARENTID, CONCAT(:VAR_URL, '/embedded', '?inlineToken=', :VAR_TOKEN, '&inlineScreenId=', (SELECT ID FROM INT_SCREEN WHERE NAME = 'Clientes')) AS MENUURL FROM DUAL",
  "menuIdColumn": "ID",
  "menuNameColumn": "NAME",
  "menuIconColumn": "ICON",
  "menuGroupColumn": "GRUPO",
  "menuParentIdColumn": "PARENTID",
  "menuUrlColumn": "MENUURL",
  "userQuery": "SELECT NOME, EMAIL, FOTO_URL FROM USUARIOS WHERE ID = :VAR_USER",
  "userName": "NOME",
  "userEmail": "EMAIL",
  "userAvatar": "FOTO_URL",
  "userMenu": "[{\\\"nome\\\":\\\"Minha Conta\\\",\\\"icone\\\":\\\"User\\\",\\\"tipoInteracao\\\":\\\"form\\\",\\\"idInteracao\\\":1},{\\\"nome\\\":\\\"Sair\\\",\\\"icone\\\":\\\"LogOut\\\",\\\"tipoInteracao\\\":\\\"action\\\",\\\"idInteracao\\\":99}]",
  "primaryColor": "#3B82F6"
}
```

#### Exemplo 2: Usando menuItems (Compatibilidade)

```json
{
  "logo": "Sistema Mitra",
  "logoIcon": "S",
  "menuItems": "[{\"id\":\"dashboard\",\"label\":\"Dashboard\",\"icon\":\"dashboard\",\"screenId\":\"10\",\"badge\":\"3\"},{\"id\":\"clientes\",\"label\":\"Clientes\",\"icon\":\"user\",\"screenId\":\"15\"},{\"id\":\"relatorios\",\"label\":\"Relatórios\",\"icon\":\"chart\",\"submenu\":[{\"id\":\"rel-vendas\",\"label\":\"Vendas\",\"screenId\":\"20\"},{\"id\":\"rel-financeiro\",\"label\":\"Financeiro\",\"screenId\":\"21\"}]},{\"id\":\"configuracoes\",\"label\":\"Configurações\",\"icon\":\"settings\",\"screenId\":\"25\"}]",
  "userInfo": "{\"name\":\"João Silva\",\"email\":\"joao@email.com\",\"role\":\"Administrador\",\"initials\":\"JS\"}",
  "userMenu": "[{\\\"nome\\\":\\\"Sair\\\",\\\"icone\\\":\\\"LogOut\\\",\\\"tipoInteracao\\\":\\\"action\\\",\\\"idInteracao\\\":99}]",
  "actionLogoutId": "5",
  "primaryColor": "#3B82F6"
}
```

### Exemplo de Acesso no Código

```javascript
// As variáveis já estão disponíveis globalmente via componentData

// Cabeçalho
const headerTitle = componentData.headerTitle || componentData.logo;
const headerSubtitle = componentData.headerSubtitle;

// Menu - Verifica se usa menuQuery ou menuItems
if (componentData.menuQuery) {
  // Usando menuQuery (SQL)
  const menuQuery = componentData.menuQuery;
  const menuIdColumn = componentData.menuIdColumn;
  // ... outros mapeamentos
} else if (componentData.menuItems) {
  // Usando menuItems (JSON)
  const menuItems = JSON.parse(componentData.menuItems);
}

// Usuário - Verifica se usa userQuery ou userInfo
if (componentData.userQuery) {
  // Usando userQuery (SQL)
  const userQuery = componentData.userQuery;
  const userName = componentData.userName;
} else if (componentData.userInfo) {
  // Usando userInfo (JSON)
  const userInfo = JSON.parse(componentData.userInfo);
}

// Menu do usuário
if (componentData.userMenu) {
  const userMenu = JSON.parse(componentData.userMenu);
}
```

## Dependências

### Funções Mitra Utilizadas

- `modalMitra({ id, parent, width, height })` - Carrega telas do Mitra em iframe interno usando o parâmetro `parent` (ID do elemento container)
- `actionMitra({ id })` - Executa Actions do Mitra (fallback para navegação via Action)
- `setVariableMitra({ name, content })` - Define variáveis globais do Mitra antes de navegar
- `queryMitra(query)` - Executa queries SQL para:
  - Carregar dados do menu e do usuário
  - Carregar opções dinâmicas de campos select/multiselect/year
  - Recuperar valores salvos de variáveis `:VAR_` para pré-preencher filtros (usando `SELECT :VAR_NOME as value FROM dual`)

### DBActions Necessárias

Nenhuma DBAction é necessária diretamente. O componente usa Actions e telas do Mitra.

### Actions Necessárias

- **ID:** Configurado em `componentData.actionLogoutId`
  - **Descrição:** Action que executa o logout do usuário
  - **Variáveis Utilizadas:** Nenhuma (ou conforme sua Action configurada)

- **IDs:** Configurados em `actionId` de cada item do menu
  - **Descrição:** Actions que navegam para telas em iframe
  - **Variáveis Utilizadas:** Conforme `contextVariable` configurado em cada item
  - **Como Configurar:** A Action deve incluir um passo de navegação que carrega a tela desejada no iframe atual (não em modal)

### Variáveis Globais Utilizadas

- `:VAR_TELA_CONTEXTO` (ou variável customizada via `contextVariable` no item do menu)
  - **Uso:** Definida antes de abrir modais para passar contexto
  - **Quando:** Antes de chamar `modalMitra()` se o item tiver `contextVariable` configurado

- **Variáveis de Filtros:** Definidas dinamicamente conforme configuração em `filtersByScreen`
  - **Uso:** Usadas em queries SQL de componentes dentro do iframe para filtrar dados
  - **Quando:** Definidas automaticamente ao aplicar filtros via FAB
  - **Formato:**

    - Text/Select: valor único
    - Multiselect/Checkbox: valores separados por vírgula (ex: "1,2,3")
    - DateRange: duas variáveis (início e fim) no formato YYYY-MM-DD

### Telas Utilizadas

- **IDs:** Extraídos de URLs embedded (`/embedded?inlineScreenId=123`) ou do campo `screenId` de cada item do menu
  - **Uso:** Carregadas em iframe interno via `modalMitra({ id, parent: 'mitra-main-content', width: 100, height: 100 })`
  - **Contexto:** Cada item pode carregar uma tela diferente substituindo o conteúdo principal
  - **Container:** As telas são renderizadas dentro do elemento `#mitra-main-content` no conteúdo principal

## Funcionalidades

### 1. Colapsar/Expandir Sidebar

A sidebar pode ser colapsada clicando no botão no header. Quando colapsada:

- Largura reduz para 80px
- Textos são ocultados
- Apenas ícones são exibidos
- Tooltips aparecem ao passar o mouse

### 2. Busca de Itens

O campo de busca filtra os itens do menu em tempo real, incluindo subitens. A busca é case-insensitive e busca pelo texto do label.

### 3. Badges/Contadores

Itens podem exibir badges com contadores. No estado colapsado, badges são exibidos como pequenos círculos no canto superior direito do ícone.

### 4. Submenus

Itens podem ter submenus expansíveis. Ao clicar no item pai, o submenu expande/recolhe. Subitens são exibidos com indentação e um indicador visual.

### 5. Estado Ativo

O item ativo é destacado visualmente com fundo azul claro e texto azul escuro. O estado ativo é gerenciado automaticamente ao clicar em itens.

### 6. Perfil do Usuário

A seção de perfil exibe:

- Avatar (imagem ou iniciais geradas automaticamente)
- Nome completo
- E-mail do usuário
- Indicador de status online (ponto verde)
- Dropdown com ações do perfil (userMenu)

No estado colapsado, apenas o avatar é exibido com tooltip.

### 7. Menu de Ações do Perfil (userMenu)

Dropdown que aparece ao clicar no perfil do usuário. Pode conter múltiplas ações como "Minha Conta", "Configurações", "Sair", etc. Cada ação pode ser:

- Formulário (`tipoInteracao: "form"`)
- Action do Mitra (`tipoInteracao: "action"`)
- Modal (`tipoInteracao: "modal"`)

**Importante:** Deve conter pelo menos uma ação de "Sair" para logout.

### 8. Agrupamento de Itens

Quando `menuGroupColumn` está configurado, os itens são organizados em grupos/seções. Cada grupo é exibido com um cabeçalho na sidebar.

### 9. Logout

O logout pode ser configurado de duas formas:

1. **Via userMenu (recomendado):** Adicione uma ação "Sair" no `userMenu`
2. **Via actionLogoutId (compatibilidade):** Configure `actionLogoutId` diretamente

### 10. Sistema de Filtros por Tela

O componente inclui um botão flutuante (FAB) no canto inferior direito que abre um modal lateral deslizante com filtros customizáveis.

**Características:**

- **FAB Flutuante:** Botão circular fixo no canto inferior direito
- **Modal Lateral:** Desliza da direita ao clicar no FAB
- **Filtros Dinâmicos:** Cada tela pode ter sua própria configuração de filtros
- **Badge de Contador:** Exibe quantidade de filtros ativos
- **Persistência:** Valores são salvos em variáveis globais Mitra
- **Recuperação Automática:** Ao abrir o modal, valores previamente salvos são automaticamente recuperados e campos são pré-preenchidos
- **Recarga Automática:** Tela atual é recarregada após aplicar filtros

**Tipos de Campos:**

- Campos de texto para busca livre
- Dropdowns para seleção única
- Multiselect para múltipla seleção com busca (ideal para listas longas)
- Checkboxes para múltipla seleção (ideal para listas curtas)
- Seletores de período (data início/fim)

**Uso:** Configure `filtersByScreen` no `componentData` mapeando `screenId` para configuração de filtros. As variáveis definidas podem ser usadas em queries SQL de componentes dentro do iframe.

## Ícones Disponíveis

O componente suporta ícones da biblioteca Lucide React. Os ícones podem ser especificados em:

- **PascalCase** (padrão do Menu nativo): `LayoutDashboard`, `Users`, `PieChart`, `LogOut`
- **lowercase** (compatibilidade): `home`, `dashboard`, `user`, `settings`

O componente converte automaticamente PascalCase para lowercase.

### Ícones Suportados

- `home` / `Home` - Ícone de casa
- `dashboard` / `LayoutDashboard` - Ícone de dashboard/gráficos
- `user` / `User` / `Users` - Ícone de usuário(s)
- `settings` / `Settings` - Ícone de configurações
- `bell` / `Bell` - Ícone de notificações
- `file` / `FileText` - Ícone de arquivo/documento
- `chart` / `BarChart3` / `PieChart` - Ícone de gráfico/analytics
- `logout` / `LogOut` - Ícone de logout

**Nota:** Para usar outros ícones Lucide, especifique o nome em PascalCase (ex: `ShoppingCart`, `CreditCard`, `Database`). O componente normaliza automaticamente.

## Exemplos

### Exemplo Básico

```json
{
  "logo": "Minha App",
  "logoIcon": "M",
  "menuItems": "[{\"id\":\"home\",\"label\":\"Início\",\"icon\":\"home\",\"screenId\":\"1\"},{\"id\":\"dashboard\",\"label\":\"Dashboard\",\"icon\":\"dashboard\",\"screenId\":\"2\"}]",
  "userInfo": "{\"name\":\"Usuário Teste\",\"role\":\"Admin\",\"initials\":\"UT\"}",
  "actionLogoutId": "1"
}
```

### Exemplo com Submenus e Badges

```json
{
  "logo": "Sistema Completo",
  "logoIcon": "S",
  "menuItems": "[{\"id\":\"dashboard\",\"label\":\"Dashboard\",\"icon\":\"dashboard\",\"screenId\":\"10\"},{\"id\":\"notificacoes\",\"label\":\"Notificações\",\"icon\":\"bell\",\"screenId\":\"15\",\"badge\":\"5\"},{\"id\":\"relatorios\",\"label\":\"Relatórios\",\"icon\":\"chart\",\"submenu\":[{\"id\":\"rel-vendas\",\"label\":\"Vendas\",\"screenId\":\"20\"},{\"id\":\"rel-financeiro\",\"label\":\"Financeiro\",\"screenId\":\"21\",\"badge\":\"2\"}]}]",
  "userInfo": "{\"name\":\"Maria Santos\",\"role\":\"Gerente\",\"initials\":\"MS\"}",
  "actionLogoutId": "5"
}
```

### Exemplo com Variável de Contexto

```json
{
  "menuItems": "[{\"id\":\"cliente-detalhes\",\"label\":\"Ver Cliente\",\"icon\":\"user\",\"screenId\":\"30\",\"contextVariable\":\":VAR_CLIENTE_ID\",\"contextValue\":\"123\"}]"
}
```

Neste caso, antes de abrir o modal, o componente define a variável `:VAR_CLIENTE_ID` com o valor especificado em `contextValue` (ou `item.id` se não especificado).

### Exemplo com Navegação em Iframe Interno

#### Via menuQuery (URLs Embedded - Recomendado)

```json
{
  "menuQuery": "SELECT 1 AS ID, 'Dashboard' AS NAME, 'LayoutDashboard' AS ICON, 'Principal' AS GRUPO, '' AS PARENTID, CONCAT(:VAR_URL, '/embedded', '?inlineToken=', :VAR_TOKEN, '&inlineScreenId=', (SELECT ID FROM INT_SCREEN WHERE NAME = 'Dashboard')) AS MENUURL FROM DUAL",
  "menuIdColumn": "ID",
  "menuNameColumn": "NAME",
  "menuIconColumn": "ICON",
  "menuUrlColumn": "MENUURL"
}
```

As URLs no formato `/embedded?inlineToken=...&inlineScreenId=...` são automaticamente detectadas. A função `resolveScreenId()` extrai o ID da tela e carrega dentro do container `#mitra-main-content` usando `modalMitra({ id, parent: 'mitra-main-content', width: 100, height: 100 })`.

#### Via menuItems (screenId direto)

```json
{
  "menuItems": "[{\"id\":\"dashboard\",\"label\":\"Dashboard\",\"icon\":\"dashboard\",\"screenId\":\"10\"},{\"id\":\"clientes\",\"label\":\"Clientes\",\"icon\":\"user\",\"screenId\":\"15\"}]"
}
```

Neste caso, o `screenId` é extraído diretamente e a tela é carregada em iframe interno. O componente faz "hard reset" do container antes de carregar cada nova tela.

#### Via menuItems (actionId - Fallback)

```json
{
  "menuItems": "[{\"id\":\"dashboard\",\"label\":\"Dashboard\",\"icon\":\"dashboard\",\"actionId\":\"10\",\"contextVariable\":\":VAR_TELA_CONTEXTO\"},{\"id\":\"clientes\",\"label\":\"Clientes\",\"icon\":\"user\",\"actionId\":\"15\"}]"
}
```

Neste caso, ao clicar nos itens, a Action é executada via `actionMitra()`. A Action deve estar configurada no Mitra para navegar para a tela desejada. A variável `:VAR_TELA_CONTEXTO` será definida antes de executar a Action.

## Navegação e Integração com Mitra

### Como Funciona a Navegação

O componente carrega telas Mitra **dentro de um iframe interno**, substituindo o conteúdo principal ao invés de abrir modais sobrepostos. Isso cria uma experiência de navegação fluida similar a uma SPA (Single Page Application).

Quando um usuário clica em um item do menu:

1. **Se o item tem submenu:** Apenas expande/recolhe o submenu
2. **Se o item tem URL no formato embedded (`/embedded?inlineToken=...&inlineScreenId=123`):**
   - A função `resolveScreenId()` extrai o ID numérico da tela da URL
   - Faz "hard reset" do container (remove o container anterior e cria um novo)
   - Carrega a tela dentro do container `#mitra-main-content` usando `modalMitra({ id, parent: 'mitra-main-content', width: 100, height: 100 })`
   - **Prioridade máxima:** URLs embedded têm precedência sobre outras formas de navegação
3. **Se o item tem `screenId` (valor numérico direto):**
   - Extrai o ID usando `resolveScreenId()`
   - Faz "hard reset" do container
   - Carrega a tela dentro do container usando `modalMitra({ id, parent: 'mitra-main-content', width: 100, height: 100 })`
4. **Se o item tem `actionId`:**
   - Define variável de contexto (se `contextVariable` configurado)
   - Executa Action via `actionMitra({ id: actionId })` como fallback
   - **Nota:** Actions devem estar configuradas para navegar para telas em iframe
5. **Se o item tem `query`:**
   - Executa query via `queryMitra(query)`
   - Se `onQueryResult` estiver configurado, chama função JavaScript customizada

#### Hard Reset do Container

Antes de carregar uma nova tela, o componente executa um "hard reset":

1. Remove o container anterior (`#mitra-main-content`) completamente
2. Cria um novo container com o mesmo ID
3. Carrega a nova tela dentro do novo container

Isso garante que:

- Não há conflitos entre telas anteriores e novas
- O estado é completamente limpo entre navegações
- A performance é otimizada removendo elementos não utilizados

#### Função resolveScreenId

O componente inclui uma função `resolveScreenId()` que extrai IDs de telas de diferentes formatos:

- **URLs embedded:** `/embedded?inlineToken=...&inlineScreenId=123` → retorna `123`
- **Valores diretos:** `"123"` → retorna `123`
- **Valores inválidos:** retorna `null`

#### Navegação em Iframe Interno (Padrão)

**Todas as telas são carregadas em iframe interno:**

- A tela é renderizada dentro do elemento `#mitra-main-content` no conteúdo principal
- A tela anterior é completamente substituída (não há sobreposição)
- Use para navegação principal entre seções da aplicação
- **Formas de configurar:**
  1. **URLs embedded (recomendado):** Use `menuQuery` com URLs no formato `/embedded?inlineToken=...&inlineScreenId=...`
  2. **Via screenId direto:** Configure `screenId` com o ID numérico da tela (extraído automaticamente de URLs ou valor direto)
  3. **Via Action (fallback):** Configure `actionId` e crie uma Action no Mitra que navegue para a tela desejada

### Regras Importantes

- **Nunca use navegação tradicional:** Não use `<a href>` ou `window.location` - isso quebraria a estrutura de navegação da plataforma
- **Sempre use funções Mitra:** Use `modalMitra({ id, parent })` para iframe interno, `actionMitra()` ou `setVariableMitra()`
- **Parâmetro `parent` é obrigatório:** Para carregar telas em iframe interno, sempre use `modalMitra({ id, parent: 'mitra-main-content', width: 100, height: 100 })`
- **Hard Reset automático:** O componente faz "hard reset" do container automaticamente antes de carregar cada nova tela
- **Variáveis devem começar com `:VAR_`:** Ao definir variáveis globais, sempre use o prefixo `:VAR_`
- **IDs são números:** A função `resolveScreenId()` converte automaticamente strings para números
- **URLs embedded são preferenciais:** Use URLs no formato `/embedded?inlineScreenId=123` para máxima compatibilidade

## Estilização

O componente usa Tailwind CSS e segue o tema claro padrão:

- **Background:** Branco (`bg-white`)
- **Bordas:** Cinza claro (`border-slate-200`)
- **Texto primário:** Cinza escuro (`text-slate-800`)
- **Texto secundário:** Cinza médio (`text-slate-500`)
- **Hover:** Cinza muito claro (`bg-slate-50`)
- **Item ativo:** Azul claro (`bg-blue-50 text-blue-700`)
- **Badges:** Azul claro (`bg-blue-100 text-blue-700`)

### Dimensões

- **Expandida:** 260px (`w-64`) - reduzida em ~10%
- **Colapsada:** 80px (`w-20`)
- **Transição:** 300ms com easing suave

## Constantes e Configurações Internas

O componente utiliza um objeto `CONFIG` centralizado que contém todas as constantes de configuração. Essas constantes podem ser úteis para desenvolvedores que desejam entender ou modificar o comportamento interno do componente.

### Estrutura do Objeto CONFIG

```javascript
const CONFIG = {
  SIDEBAR: {
    WIDTH_EXPANDED: 288,        // Largura da sidebar expandida (w-72 em pixels)
    WIDTH_COLLAPSED: 80,        // Largura da sidebar colapsada (w-20 em pixels)
    TRANSITION_DURATION: 300,   // Duração da transição de colapso/expansão (ms)
  },
  DROPDOWN: {
    MAX_HEIGHT: 300,            // Altura máxima do dropdown de multiselect (px)
    ANIMATION_DURATION: 150,    // Duração da animação de abertura/fechamento (ms)
  },
  DEBOUNCE: {
    SEARCH: 300,                // Delay para busca de itens do menu (ms)
    MULTISELECT_SEARCH: 300,    // Delay para busca dentro de multiselect (ms)
  },
  FILTER: {
    FAB_SIZE: 56,              // Tamanho do botão flutuante de filtros (w-14 h-14 em px)
    DRAWER_MAX_WIDTH: 448,     // Largura máxima do drawer de filtros (max-w-md em pixels)
    FOCUS_DELAY: 50,            // Delay para focar em inputs após abertura (ms)
  },
  TIMING: {
    ICON_UPDATE_DELAY: 100,    // Delay para atualização de ícones (ms)
    FILTER_RECOVERY_DELAY: 200, // Delay para recuperação de filtros salvos (ms)
    TRANSITION_CLEANUP: 350,   // Delay para limpeza de classes de transição (300ms animação + margem)
  }
};
```

### Descrição das Constantes

#### SIDEBAR

| Constante | Valor | Descrição |
|-----------|-------|-----------|
| `WIDTH_EXPANDED` | 288px | Largura da sidebar quando expandida. Corresponde à classe Tailwind `w-72` |
| `WIDTH_COLLAPSED` | 80px | Largura da sidebar quando colapsada. Corresponde à classe Tailwind `w-20` |
| `TRANSITION_DURATION` | 300ms | Duração da animação de transição entre estados expandido/colapsado |

#### DROPDOWN

| Constante | Valor | Descrição |
|-----------|-------|-----------|
| `MAX_HEIGHT` | 300px | Altura máxima do container de opções do multiselect antes de ativar scroll |
| `ANIMATION_DURATION` | 150ms | Duração da animação de abertura/fechamento de dropdowns |

#### DEBOUNCE

| Constante | Valor | Descrição |
|-----------|-------|-----------|
| `SEARCH` | 300ms | Tempo de espera após o usuário parar de digitar antes de executar a busca no menu |
| `MULTISELECT_SEARCH` | 300ms | Tempo de espera após o usuário parar de digitar antes de filtrar opções no multiselect |

**Nota:** O debounce melhora a performance evitando execuções excessivas durante a digitação.

#### FILTER

| Constante | Valor | Descrição |
|-----------|-------|-----------|
| `FAB_SIZE` | 56px | Tamanho do botão flutuante (FAB) de filtros. Corresponde às classes Tailwind `w-14 h-14` |
| `DRAWER_MAX_WIDTH` | 448px | Largura máxima do drawer lateral que contém os filtros. Corresponde à classe Tailwind `max-w-md` |
| `FOCUS_DELAY` | 50ms | Delay aplicado antes de focar automaticamente em inputs quando o drawer de filtros é aberto |

#### TIMING

| Constante | Valor | Descrição |
|-----------|-------|-----------|
| `ICON_UPDATE_DELAY` | 100ms | Delay para atualização de ícones após mudanças de estado |
| `FILTER_RECOVERY_DELAY` | 200ms | Delay para recuperação automática de valores de filtros salvos após carregar uma tela |
| `TRANSITION_CLEANUP` | 350ms | Delay para remoção de classes CSS de transição após animações (300ms de animação + 50ms de margem de segurança) |

### Uso das Constantes no Código

Todas as constantes são acessadas através do objeto `CONFIG`:

```javascript
// Exemplo: Usando constante de debounce
searchTimeout = setTimeout(() => {
  // Lógica de busca
}, CONFIG.DEBOUNCE.SEARCH);

// Exemplo: Usando constante de delay de foco
setTimeout(() => {
  searchInput.focus();
}, CONFIG.FILTER.FOCUS_DELAY);
```

### Benefícios da Centralização

- **Manutenibilidade:** Todas as configurações em um único local facilitam ajustes
- **Consistência:** Garante que valores relacionados sejam usados de forma uniforme
- **Documentação:** Valores ficam claramente documentados e com propósito explícito
- **Facilita Customização:** Desenvolvedores podem facilmente identificar e modificar comportamentos específicos

### Modificando Constantes

Se você precisar modificar essas constantes (por exemplo, para ajustar tempos de debounce ou dimensões), edite o objeto `CONFIG` no início do arquivo `1_sidebar.html`, dentro da tag `<script>`.

**Atenção:** Modifique apenas se tiver certeza do impacto. Alterações podem afetar a experiência do usuário e a performance do componente.

## Notas Importantes

- O componente é carregado dentro de um iframe na plataforma Mitra
- **Telas são carregadas em iframe interno:** Todas as telas são renderizadas dentro do container `#mitra-main-content` usando `modalMitra({ id, parent })`
- **Hard Reset automático:** O componente remove e recria o container antes de carregar cada nova tela para evitar conflitos
- Todas as variáveis do `componentData` chegam como strings
- Use `JSON.parse()` para objetos JSON e `parseInt()` para números
- A função `resolveScreenId()` extrai automaticamente IDs de URLs embedded ou valores diretos
- A sidebar não é responsiva (apenas desktop conforme especificado)
- Tooltips aparecem automaticamente no estado colapsado
- O estado colapsado é mantido durante a sessão (não persiste entre recarregamentos)
- A busca filtra apenas itens do primeiro nível e subitens visíveis

### Compatibilidade com Menu Nativo

O componente é **totalmente compatível** com o componente Menu nativo do Mitra:

- ✅ Suporta `menuQuery` (query SQL) - padrão do Menu nativo
- ✅ Suporta `userQuery` (query SQL) - padrão do Menu nativo
- ✅ Suporta `userMenu` (menu de ações) - padrão do Menu nativo
- ✅ Suporta URLs embedded (`/embedded?inlineToken=...`) - formato obrigatório do Menu nativo
- ✅ Suporta mapeamento de colunas (`menuIdColumn`, `menuNameColumn`, etc.)
- ✅ Suporta agrupamento via `menuGroupColumn`
- ✅ Suporta hierarquia via `menuParentIdColumn`
- ✅ Mantém compatibilidade com formato JSON (`menuItems`, `userInfo`)

**Recomendação:** Use `menuQuery` e `userQuery` para máxima compatibilidade e integração com o ecossistema Mitra.

## Troubleshooting

### Sidebar não aparece

- Verifique se o arquivo HTML está sendo carregado corretamente
- Confirme que o Tailwind CSS está acessível (CDN)

### Itens do menu não aparecem

**Se usando menuQuery:**

- Verifique se a query SQL está correta e retorna dados
- Confirme que todas as colunas obrigatórias estão mapeadas (`menuIdColumn`, `menuNameColumn`, `menuIconColumn`, `menuUrlColumn`)
- Verifique se a fórmula da URL está correta (deve usar `CONCAT(:VAR_URL, '/embedded', ...)`)
- Confirme que os nomes das telas em `INT_SCREEN.NAME` estão corretos
- Verifique o console do navegador para erros de query

**Se usando menuItems:**

- Verifique se `menuItems` está no formato JSON string correto
- Confirme que cada item tem `id`, `label` e `icon`
- Verifique o console do navegador para erros de parsing

### Navegação não funciona

- Confirme que `screenId` (em URLs embedded ou valor direto) ou `actionId` estão configurados corretamente
- Verifique se a função `resolveScreenId()` está extraindo o ID corretamente (verifique o console)
- Confirme que as telas/Actions existem na plataforma Mitra
- Verifique se o container `#mitra-main-content` existe no DOM
- Confirme que `modalMitra()` está sendo chamado com o parâmetro `parent: 'mitra-main-content'`
- Verifique erros no console relacionados a "Erro ao carregar tela Mitra"

### Badges não aparecem

- Verifique se o campo `badge` está presente no item
- Confirme que o valor é uma string (mesmo que seja um número)

### Submenus não expandem

**Se usando menuQuery:**

- Confirme que `menuParentIdColumn` está configurado
- Verifique se os IDs de parent correspondem a IDs válidos de itens
- Confirme que a coluna `PARENTID` na query está correta

**Se usando menuItems:**

- Confirme que o campo `submenu` é um array válido
- Verifique se os subitens têm `id` e `label` configurados

### Menu do usuário não aparece

- Verifique se `userMenu` está no formato JSON string correto
- Confirme que cada ação tem `nome`, `icone`, `tipoInteracao` e `idInteracao`
- Verifique se há pelo menos uma ação de "Sair" configurada
- Confirme que os ícones estão em PascalCase (ex: `User`, `LogOut`)

### Dados do usuário não aparecem

**Se usando userQuery:**

- Verifique se a query SQL retorna exatamente uma linha
- Confirme que as colunas estão mapeadas corretamente (`userName`, `userEmail`, `userAvatar`)
- Verifique se `:VAR_USER` está disponível no contexto

**Se usando userInfo:**

- Verifique se `userInfo` está no formato JSON string correto
- Confirme que contém pelo menos `name` ou `email`

### Menu recarrega quando ações são executadas nas telas

**Sintoma:**
O menu está recarregando completamente (mostrando loading) toda vez que uma ação é executada dentro de uma tela carregada no iframe. Isso acontece com qualquer tipo de ação: `dbactionMitra()`, `actionMitra()`, `modalMitra()`, `setVariableMitra()`, etc.

**Causa Raiz:**

Este problema ocorre por duas razões principais:

1. **Falta da função `updateMitra()`**: A plataforma Mitra possui um mecanismo de atualização de componentes que chama a função `updateMitra()` quando o componente precisa ser atualizado. Se essa função não existir, a plataforma recarrega o componente inteiro, causando o loading no menu.

2. **Event Listener Global Capturando Eventos do Iframe**: O event listener global de `click` estava capturando eventos que vêm de dentro do iframe onde as telas são carregadas. Quando ações são executadas dentro do iframe, esses eventos podem "vazar" para o documento pai e disparar ações indesejadas no menu.

**Solução:**

#### 1. Adicionar Função `updateMitra()` (Obrigatória)

A função `updateMitra()` deve estar presente no código do componente. Ela é chamada pela plataforma Mitra quando o componente precisa ser atualizado, evitando recarregamento completo:

```javascript
/**
 * Função chamada pela plataforma Mitra quando o componente precisa ser atualizado
 * Evita recarregamento completo do componente quando apenas componentData muda
 * Baseado no padrão do sidebar_example.html
 */
function updateMitra() {
  // Quando o componenteMitra atualizar, ao invés de atualizar o componente todo,
  // ele vai chamar esse método apenas.
  // Por enquanto, não fazemos nada aqui para evitar recarregamento desnecessário.
  // Se necessário atualizar dados específicos (ex: menu, usuário), pode ser feito aqui.
  logger.info('UpdateMitra', 'Componente atualizado sem recarregamento completo');
}
```

**Importante:** Mesmo que a função esteja vazia, ela deve existir. Sem ela, a plataforma Mitra recarrega o componente inteiro.

#### 2. Melhorar Event Listener de Click

O event listener global de `click` deve ignorar eventos que vêm de dentro do iframe. Use múltiplas verificações para garantir que eventos do iframe sejam detectados:

```javascript
// Fecha menu do usuário ao clicar fora
document.addEventListener('click', (e) => {
  // Ignora eventos que vêm de dentro do iframe (telas carregadas)
  // Verifica múltiplas formas de detectar eventos do iframe para garantir que não sejam processados
  const mitraContent = document.getElementById('mitra-main-content');
  if (mitraContent && (
    e.target.closest('#mitra-main-content') ||
    mitraContent.contains(e.target) ||
    e.target === mitraContent ||
    (e.target.tagName === 'IFRAME' && mitraContent.querySelector('iframe') === e.target)
  )) {
    return;
  }
  
  // Resto do código do listener...
});
```

**Por que múltiplas verificações?**
- `closest()` pode não funcionar em todos os casos de propagação de eventos
- `contains()` verifica se o elemento está dentro do container
- Verificação direta do elemento
- Verificação específica para tags `iframe`

**Regras Críticas para Desenvolvedores:**

1. **SEMPRE inclua `updateMitra()`**: Esta função é essencial e deve estar presente em todos os componentes customizados
2. **SEMPRE filtre eventos do iframe**: Qualquer event listener global deve verificar se o evento vem do iframe antes de processar
3. **Use múltiplas verificações**: Não confie apenas em uma forma de detectar eventos do iframe
4. **Teste ações nas telas**: Sempre teste se ações executadas dentro das telas não causam recarregamento do menu

**Como Verificar se Está Funcionando:**

1. Execute uma ação dentro de uma tela (ex: clique em um botão que executa `dbactionMitra()`)
2. Observe o menu - ele NÃO deve mostrar loading ou recarregar
3. O menu deve permanecer estável e funcional
4. Ações dentro do menu (fechar dropdowns, etc.) devem continuar funcionando normalmente

**Prevenção em Novos Componentes:**

Ao criar novos componentes customizados que serão usados dentro do Menu:

- Sempre inclua a função `updateMitra()` mesmo que vazia
- Se criar event listeners globais, sempre filtre eventos do iframe
- Teste interações entre o componente e o menu para garantir que não há conflitos

### Filtros não são recuperados automaticamente

Se os filtros não estão sendo pré-preenchidos ao abrir o modal:

- **Verifique se as variáveis existem:** Confirme que as variáveis `:VAR_` foram previamente definidas ao aplicar filtros
- **Confirme os nomes das variáveis:** Verifique se os nomes das variáveis em `filtersByScreen` correspondem exatamente às variáveis que foram salvas (incluindo o prefixo `:VAR_`)
- **Teste a query manualmente:** Execute `SELECT :VAR_NOME_VARIAVEL as value FROM dual` diretamente no Mitra para confirmar que a variável tem valor
- **Verifique o console:** Abra o console do navegador e verifique se há erros ao buscar variáveis (erros são ignorados silenciosamente, mas podem aparecer no console)
- **Confirme o tipo do campo:** Para campos `dateRange`, verifique se ambas as variáveis (`variableStart` e `variableEnd`) estão configuradas corretamente
- **Valores NULL ou vazios:** Se uma variável retornar NULL ou string vazia, o campo correspondente permanecerá vazio (comportamento esperado)
- **Recarregamento da página:** A recuperação acontece apenas ao abrir o modal, não automaticamente ao carregar a página
