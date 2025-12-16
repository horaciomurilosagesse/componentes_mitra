# Regras de Negócio e Padrões - Componentes CRM Tecnofibras

Este documento contém as regras de negócio, padrões de design e convenções de código aplicadas nos componentes CRM da Tecnofibras. Use este documento como referência ao criar ou modificar componentes similares.

## Índice

1. [Design System](#design-system)
2. [Formatação de Campos Obrigatórios](#formatação-de-campos-obrigatórios)
3. [Organização de Formulários](#organização-de-formulários)
4. [Padrões de Código Mitra](#padrões-de-código-mitra)
5. [Estrutura de Componentes](#estrutura-de-componentes)
6. [Boas Práticas](#boas-práticas)

---

## Design System

### Cores da Marca

As cores institucionais da Tecnofibras devem ser usadas consistentemente em todos os componentes:

```javascript
brand: {
    orange: '#FF6600',        // Laranja Institucional
    orangeDark: '#E55A00',    // Laranja Escuro (hover)
    blueDark: '#002855',      // Azul Escuro Institucional
    blueLight: '#3b82f6',     // Azul Claro (auxiliar)
    bg: '#F8FAFC',            // Background padrão
    border: '#E2E8F0',        // Cor de bordas
}
```

### Paleta de Cores Slate

Para elementos de interface, use a paleta Slate do Tailwind:

- **Textos principais:** `text-slate-800` (títulos), `text-slate-700` (texto normal)
- **Textos secundários:** `text-slate-600`, `text-slate-500`
- **Textos terciários:** `text-slate-400`
- **Bordas:** `border-slate-200`
- **Backgrounds:** `bg-slate-50` (cards destacados), `bg-white` (fundos principais)

### Tipografia

- **Fonte:** Inter (Google Fonts) - `font-family: 'Inter', sans-serif`
- **Títulos de seção:** `text-lg font-bold text-slate-800`
- **Labels de campos:** `text-xs font-bold text-slate-500 uppercase tracking-wide`
- **Texto normal:** `text-sm font-medium text-slate-700`

### Espaçamento

- **Gap entre seções:** `space-y-6` ou `space-y-10`
- **Gap entre campos:** `gap-6` (grid)
- **Padding em cards:** `p-6`
- **Padding em inputs:** `px-4 py-2.5`

### Bordas e Sombras

- **Border radius padrão:** `rounded-lg` (6px)
- **Border radius cards:** `rounded-lg` ou `rounded-xl`
- **Sombras:** `shadow-sm` (inputs), `shadow-md` (botões), `shadow-lg` (hover)
- **Bordas:** `border border-slate-200`

---

## Formatação de Campos Obrigatórios

### Regra

Todos os campos obrigatórios devem ter formatação visual consistente para indicar que são obrigatórios.

### Implementação

#### 1. CSS para Campos Obrigatórios

```css
/* Estilos para campos obrigatórios */
.required-asterisk {
    color: #EF4444;
    font-weight: 700;
    margin-left: 2px;
}

.required-field {
    border-left: 3px solid #EF4444 !important;
    padding-left: calc(1rem - 3px) !important;
}

.required-label {
    position: relative;
}

.required-label::after {
    content: '*';
    color: #EF4444;
    font-weight: 700;
    margin-left: 2px;
}

.required-field:focus {
    border-left-color: #EF4444 !important;
}
```

#### 2. Aplicação no HTML

**Labels:**
```html
<label class="block text-xs font-bold text-slate-500 uppercase mb-1.5 tracking-wide required-label">
    Gestor
</label>
```

**Campos (inputs/selects):**
```html
<select id="select-gestor" class="w-full rounded-lg border border-slate-200 px-4 py-2.5 text-sm text-slate-900 bg-white focus:border-brand-blueDark focus:ring-1 focus:ring-brand-blueDark focus:outline-none transition-all shadow-sm required-field">
    <!-- options -->
</select>
```

### Campos Obrigatórios Padrão

Na tela de etapa comercial, os seguintes campos são obrigatórios:

- **Gestor** *
- **Prioridade** *
- **Cliente** *
- **Atual/Novo** *
- **Acordo de Confidencialidade** *
- **Manual de Qualidade do Fornecedor** *

---

## Organização de Formulários

### Estrutura de Seções com Ícones

Cada seção do formulário deve seguir esta estrutura com ícone:

```html
<section class="border-t-2 border-slate-200 pt-8 mt-8">
    <div class="flex items-center gap-2 mb-6 pb-3 border-b-2 border-slate-200">
        <i class="ph ph-package text-brand-blueDark text-xl"></i>
        <h3 class="text-lg font-bold text-slate-800">Título da Seção</h3>
    </div>
    <div class="space-y-6">
        <!-- Conteúdo da seção -->
    </div>
</section>
```

**Ícones Recomendados por Tipo de Seção:**
- **Informações Peça:** `ph-package`
- **Análise de Processos:** `ph-gear`
- **Informações Projeto:** `ph-folder`
- **Documentos Fornecidos:** `ph-file-doc`
- **Informações Orçamento:** `ph-calculator`
- **Informações Principais:** `ph-info`
- **Dados Comerciais:** `ph-currency-dollar`
- **Documentação:** `ph-file-text`
- **Condições:** `ph-handshake`

### Organização em Cards/Seções Agrupadas

Para formulários extensos, agrupe campos relacionados em cards visuais:

```html
<!-- Card: Informações Principais -->
<div class="bg-slate-50 rounded-xl p-6 border border-slate-200 mb-6">
    <div class="flex items-center gap-2 mb-4">
        <i class="ph ph-info text-brand-blueDark text-lg"></i>
        <h3 class="text-base font-bold text-slate-800">Informações Principais</h3>
    </div>
    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
        <!-- Campos aqui -->
    </div>
</div>

<!-- Card: Dados Comerciais -->
<div class="bg-slate-50 rounded-xl p-6 border border-slate-200 mb-6">
    <div class="flex items-center gap-2 mb-4">
        <i class="ph ph-currency-dollar text-brand-blueDark text-lg"></i>
        <h3 class="text-base font-bold text-slate-800">Dados Comerciais</h3>
    </div>
    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
        <!-- Campos aqui -->
    </div>
</div>
```

**Características dos Cards:**
- **Fundo:** `bg-slate-50` - destaca do fundo branco
- **Border radius:** `rounded-xl` - mais arredondado que inputs
- **Padding:** `p-6` - espaçamento generoso
- **Borda:** `border border-slate-200`
- **Espaçamento entre cards:** `mb-6`
- **Título com ícone:** Ícone `text-brand-blueDark` + título `text-base font-bold`

### Agrupamento de Campos

#### 1. Campos Simples (2 colunas)

```html
<div class="grid grid-cols-1 md:grid-cols-2 gap-6">
    <div>
        <label class="form-label">Campo 1</label>
        <input type="text" class="form-input" placeholder="Placeholder">
    </div>
    <div>
        <label class="form-label">Campo 2</label>
        <input type="text" class="form-input" placeholder="Placeholder">
    </div>
</div>
```

#### 2. Campo de Largura Total

```html
<div>
    <label class="form-label">Descrição</label>
    <textarea class="form-textarea" rows="3" placeholder="Descreva..."></textarea>
</div>
```

#### 3. Cards Destacados (para grupos de checkboxes)

```html
<div class="bg-slate-50 p-6 rounded-lg border border-slate-200">
    <label class="form-label mb-3">Título do Grupo:</label>
    <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
        <label class="flex items-center gap-2 cursor-pointer">
            <input type="checkbox" class="custom-checkbox">
            <span class="text-sm font-medium text-slate-700">Opção 1</span>
        </label>
        <!-- mais opções -->
    </div>
</div>
```

#### 4. Grid de 3 Colunas (para volumes, anos, etc.)

```html
<div class="bg-slate-50 p-6 rounded-lg border border-slate-200">
    <label class="form-label mb-3">Volumes (por ano):</label>
    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <div>
            <label class="block text-xs font-semibold text-slate-600 mb-1.5">1º ano</label>
            <input type="text" class="form-input" placeholder="0">
        </div>
        <!-- mais campos -->
    </div>
</div>
```

### Ordem de Campos Recomendada

1. **Identificação:** Part number, Nº SO (lado a lado)
2. **Descrição:** Campo de texto/textarea (largura total)
3. **Informações relacionadas:** Cliente, Projeto (lado a lado)
4. **Grupos de opções:** Em cards destacados
5. **Datas:** Data e Data Retorno (lado a lado)
6. **Observações:** Textarea (largura total)

---

## Padrões de Código Mitra

### Função queryMitra

**IMPORTANTE:** `queryMitra` recebe a query **diretamente como string**, não como objeto.

**❌ ERRADO:**
```javascript
const result = await queryMitra({ query: componentData.queryData });
```

**✅ CORRETO:**
```javascript
const result = await queryMitra(componentData.queryData);
```

### População de Dropdowns

Padrão para popular dropdowns a partir do banco:

```javascript
async function populateGestorDropdown() {
    try {
        const selectElement = document.getElementById('select-gestor');
        if (!selectElement) {
            console.error("Elemento select-gestor não encontrado.");
            return;
        }
        
        // Verifica se queryGestores está configurado
        if (!componentData.queryGestores || componentData.queryGestores.trim() === '') {
            console.error("Query de gestores não configurada. Configure queryGestores no editor Mitra.");
            selectElement.innerHTML = '<option value="">Query não configurada</option>';
            return;
        }
        
        // queryMitra recebe a query diretamente como string
        const result = await queryMitra(componentData.queryData);
        
        if (!result || !result.data || result.data.length === 0) {
            console.warn("Nenhum gestor encontrado no banco de dados.");
            selectElement.innerHTML = '<option value="">Nenhum gestor encontrado</option>';
            return;
        }
        
        const gestores = mapQueryToObject(result);

        selectElement.innerHTML = '';

        const defaultOption = document.createElement('option');
        defaultOption.value = '';
        defaultOption.textContent = 'Selecione um gestor';
        selectElement.appendChild(defaultOption);

        gestores.forEach(gestor => {
            const option = document.createElement('option');
            option.value = gestor.ID || '';
            option.textContent = gestor.NOME || 'Sem nome';
            selectElement.appendChild(option);
        });

    } catch (error) {
        console.error("Erro ao popular o dropdown de gestores:", error);
        const selectElement = document.getElementById('select-gestor');
        if (selectElement) {
            selectElement.innerHTML = '<option value="">Erro ao carregar gestores</option>';
        }
    }
}
```

### Função mapQueryToObject

Sempre use esta função para converter resultados de `queryMitra`:

```javascript
function mapQueryToObject(queryResult) {
    if (!queryResult || !queryResult.data || !queryResult.headers) return [];
    const headers = queryResult.headers.map(h => h.name.toUpperCase());
    return queryResult.data.map(row => {
        const obj = {};
        headers.forEach((header, index) => {
            obj[header] = row[index];
        });
        return obj;
    });
}
```

### Salvamento de Dados

Padrão para salvar dados usando variáveis Mitra:

```javascript
async function saveData() {
    const btn = document.getElementById('save-button');
    const originalHTML = btn.innerHTML;
    try {
        btn.disabled = true;
        btn.innerHTML = `<i class="ph ph-spinner animate-spin"></i> Salvando...`;

        // Define variáveis Mitra
        await setVariableMitra({ name: ':VAR_CAMPO1', content: document.getElementById('input-campo1').value });
        await setVariableMitra({ name: ':VAR_CAMPO2', content: document.getElementById('input-campo2').value });
        
        // Executa DBAction
        await dbactionMitra(parseInt(componentData.dbActionId, 10));
        
        // Recarrega dados
        await loadAndPopulateData();
        
    } catch (error) {
        console.error("Erro ao salvar os dados:", error);
    } finally {
        btn.disabled = false;
        btn.innerHTML = originalHTML;
    }
}
```

### Inicialização de Componente

Padrão para inicialização:

```javascript
async function initializeComponent() {
    await populateGestorDropdown(); // Se houver dropdowns
    await loadAndPopulateData();
    setupAnexosListeners(); // Se houver anexos
    switchTab('cabecalho'); // Se houver tabs
}

document.addEventListener('DOMContentLoaded', () => {
    document.body.addEventListener(MITRA_UPDATE_EVENT_NAME, loadAndPopulateData);

    const waitForMitraAndInit = () => {
        const maxWaitTime = 5000;
        const intervalTime = 100;
        const startTime = Date.now();
        
        const checkInterval = setInterval(() => {
            if (window.componentData && 
                window.componentData.queryData && 
                window.componentData.queryGestores && 
                window.componentData.queryAnexos && 
                window.componentData.dbActionUpsertId && 
                window.componentData.dbActionFormComercialId) {
                clearInterval(checkInterval);
                initializeComponent();
            } else if (Date.now() - startTime > maxWaitTime) {
                clearInterval(checkInterval);
                console.error("Erro: As configurações do componente (componentData) não foram carregadas a tempo.");
                document.body.innerHTML = `<div class="p-8 text-center text-red-700 bg-red-50 h-full flex items-center justify-center">Erro Crítico: As configurações do componente não foram carregadas da plataforma.</div>`;
            }
        }, intervalTime);
    };
    
    waitForMitraAndInit();
});
```

---

## Estrutura de Componentes

### Layout Padrão

```html
<body class="h-screen flex flex-col overflow-hidden">
    <!-- Header Fixo -->
    <header class="bg-white border-b-2 border-brand-border h-16 flex items-center justify-between px-6 shrink-0 z-20 shadow-md">
        <div class="flex items-center gap-4">
            <!-- Botão toggle sidebar (mobile) -->
            <button id="sidebar-toggle" class="lg:hidden p-2 rounded-lg hover:bg-slate-100 transition-colors duration-200" onclick="toggleSidebar()">
                <i class="ph ph-list text-slate-600 text-xl"></i>
            </button>
            <!-- Badge de contexto (opcional) -->
            <div class="hidden md:flex items-center gap-2 text-xs font-semibold text-slate-600 bg-slate-50 px-3 py-1.5 rounded-full border border-slate-200">
                <i class="ph ph-briefcase text-brand-orange text-sm"></i>
                <span>CRM - Comercial</span>
            </div>
        </div>
        
        <!-- Botões de Ação no Header -->
        <div class="flex items-center gap-3">
            <button id="save-button" onclick="saveData()" class="bg-brand-orange hover:bg-brand-orangeDark text-white text-sm font-semibold py-2 px-5 rounded-lg shadow-md hover:shadow-lg transition-all flex items-center gap-2">
                <i class="ph ph-floppy-disk"></i> Salvar
            </button>
            <!-- Outros botões de ação aqui -->
        </div>
    </header>

    <div class="flex flex-1 overflow-hidden relative">
        <!-- Overlay para mobile -->
        <div id="sidebar-overlay" class="hidden lg:hidden fixed inset-0 bg-black/40 backdrop-blur-sm z-20" onclick="toggleSidebar()"></div>
        
        <!-- Sidebar com Destaque Visual -->
        <aside id="sidebar" class="w-80 bg-gradient-to-b from-slate-50 to-white border-r-2 border-brand-blueDark flex-shrink-0 overflow-y-auto hidden lg:block shadow-lg">
            <!-- Header da Sidebar -->
            <div class="p-6 border-b border-slate-200 bg-white">
                <h2 class="text-xs font-bold text-slate-400 uppercase tracking-wide mb-1">Item do Kanban</h2>
                <h3 class="text-lg font-bold text-slate-800" id="sidebar-title">Título do Item</h3>
            </div>
            
            <!-- Cards de Informações -->
            <div class="p-6 space-y-4 pb-24">
                <div class="bg-white rounded-lg p-4 shadow-sm border border-slate-100">
                    <div class="text-xs font-bold text-slate-400 uppercase mb-1 tracking-wide">Descrição</div>
                    <div class="text-sm font-semibold text-slate-800">Conteúdo</div>
                </div>
                <!-- Mais cards -->
            </div>
        </aside>

        <!-- Main Content -->
        <main class="flex-1 overflow-y-auto bg-white flex flex-col h-full">
            <!-- Header de Contexto -->
            <div class="border-b border-slate-200 bg-slate-50 px-6 py-4 shrink-0">
                <div class="flex items-center gap-2 text-sm text-slate-600">
                    <i class="ph ph-folder-open text-brand-blueDark"></i>
                    <span class="font-medium">Itens Filhos da Solicitação</span>
                </div>
            </div>
            
            <!-- Tabs Navigation -->
            <div class="border-b-2 border-slate-200 bg-white shrink-0">
                <nav class="-mb-px flex space-x-8 px-6">
                    <!-- Tabs aqui -->
                </nav>
            </div>
            
            <!-- Conteúdo das tabs -->
            <div class="p-8 pb-28">
                <!-- Conteúdo -->
            </div>
        </main>
    </div>
</body>
```

**⚠️ REGRA IMPORTANTE: Não usar Footer**

**NÃO** adicione footer nos componentes. Todos os botões de ação devem estar no **header**, ao lado do botão "Salvar".

**✅ CORRETO:**
```html
<header class="...">
    <div class="flex items-center gap-3">
        <button id="save-button" onclick="saveData()" class="...">
            <i class="ph ph-floppy-disk"></i> Salvar
        </button>
        <button id="btn-aprovar" onclick="aprovarCard()" class="...">
            Aprovar para Em Análise (Eng)
            <i class="ph ph-arrow-right"></i>
        </button>
    </div>
</header>
```

**❌ ERRADO:**
```html
<footer>
    <button>Aprovar</button>
</footer>
```

### Sidebar com Destaque Visual

A sidebar deve seguir este padrão para destacar o "cabeçalho do item kanban":

**Características:**
- **Largura:** `w-80` (320px) - mais larga que antes para melhor legibilidade
- **Fundo:** Gradiente `bg-gradient-to-b from-slate-50 to-white`
- **Borda:** `border-r-2 border-brand-blueDark` - borda direita destacada
- **Sombra:** `shadow-lg` - elevação visual
- **Header:** Seção branca com título "Item do Kanban" e nome do item
- **Cards internos:** Cada informação em card branco com `bg-white rounded-lg p-4 shadow-sm border border-slate-100`

**Exemplo completo:**
```html
<aside id="sidebar" class="w-80 bg-gradient-to-b from-slate-50 to-white border-r-2 border-brand-blueDark flex-shrink-0 overflow-y-auto hidden lg:block shadow-lg">
    <!-- Header da Sidebar -->
    <div class="p-6 border-b border-slate-200 bg-white">
        <h2 class="text-xs font-bold text-slate-400 uppercase tracking-wide mb-1">Item do Kanban</h2>
        <h3 class="text-lg font-bold text-slate-800" id="sidebar-title">Título do Item</h3>
    </div>
    
    <!-- Cards de Informações -->
    <div class="p-6 space-y-4 pb-24">
        <div class="bg-white rounded-lg p-4 shadow-sm border border-slate-100">
            <div class="text-xs font-bold text-slate-400 uppercase mb-1 tracking-wide">Descrição</div>
            <div id="sidebar-description" class="text-sm font-semibold text-slate-800 leading-relaxed">...</div>
        </div>
        
        <div class="bg-white rounded-lg p-4 shadow-sm border border-slate-100">
            <div class="text-xs font-bold text-slate-400 uppercase mb-1 tracking-wide">Prioridade</div>
            <div>
                <span id="sidebar-prioridade" class="inline-block text-xs font-bold px-3 py-1.5 rounded-full bg-red-100 text-red-700">Alta</span>
            </div>
        </div>
    </div>
</aside>
```

**Badge de Prioridade com Cores Dinâmicas:**
```javascript
// No JavaScript, atualizar badge com cores baseadas no valor
const prioridadeEl = document.getElementById('sidebar-prioridade');
if (prioridadeEl) {
    const prioridade = data.PRIORIDADE || 'N/A';
    prioridadeEl.innerText = prioridade;
    prioridadeEl.className = 'inline-block text-xs font-bold px-3 py-1.5 rounded-full';
    if (prioridade === 'Alta') {
        prioridadeEl.className += ' bg-red-100 text-red-700';
    } else if (prioridade === 'Média') {
        prioridadeEl.className += ' bg-yellow-100 text-yellow-700';
    } else if (prioridade === 'Baixa') {
        prioridadeEl.className += ' bg-green-100 text-green-700';
    } else {
        prioridadeEl.className += ' bg-slate-100 text-slate-700';
    }
}
```

### Header de Contexto na Área Principal

Sempre adicione um header de contexto acima das tabs para indicar que o conteúdo são "itens filhos":

```html
<!-- Header de Contexto -->
<div class="border-b border-slate-200 bg-slate-50 px-6 py-4 shrink-0">
    <div class="flex items-center gap-2 text-sm text-slate-600">
        <i class="ph ph-folder-open text-brand-blueDark"></i>
        <span class="font-medium">Itens Filhos da Solicitação</span>
    </div>
</div>
```

### Responsividade da Sidebar

**Mobile:**
- Sidebar oculta por padrão (`hidden lg:block`)
- Botão toggle no header para abrir
- Overlay escuro quando aberta
- Transição suave de abertura/fechamento

**CSS para Mobile:**
```css
@media (max-width: 1024px) {
    #sidebar {
        position: fixed;
        left: -100%;
        top: 64px;
        height: calc(100vh - 64px);
        z-index: 30;
        transition: left 0.3s ease-in-out;
        box-shadow: 2px 0 8px rgba(0, 0, 0, 0.1);
    }
    
    #sidebar.mobile-open {
        left: 0;
    }
}
```

**JavaScript para Toggle:**
```javascript
function toggleSidebar() {
    const sidebar = document.getElementById('sidebar');
    const overlay = document.getElementById('sidebar-overlay');
    if (sidebar && overlay) {
        sidebar.classList.toggle('mobile-open');
        sidebar.classList.toggle('hidden');
        overlay.classList.toggle('hidden');
    }
}
```

### Tabs Navigation

```html
<div class="border-b-2 border-slate-200 bg-white shrink-0">
    <nav class="-mb-px flex space-x-8 px-6">
        <span id="tab-cabecalho" class="cursor-pointer border-b-2 border-brand-blueDark text-brand-blueDark py-4 px-1 text-sm font-semibold hover:text-brand-orange transition-colors duration-200" onclick="switchTab('cabecalho')">
            Cabeçalho
        </span>
        <span id="tab-formulario" class="cursor-pointer border-b-2 border-transparent text-slate-500 py-4 px-1 text-sm font-semibold hover:text-brand-orange transition-colors duration-200" onclick="switchTab('formulario')">
            Formulário
        </span>
    </nav>
</div>
```

**Mudanças:**
- **Borda:** `border-b-2` (mais espessa) em vez de `border-b`
- **Padding horizontal:** `px-6` no nav para alinhar com conteúdo
- **Background:** `bg-white` explícito para garantir contraste

### Função switchTab

```javascript
function switchTab(tabName) {
    const contents = ['cabecalho', 'formulario', 'anexos'];
    contents.forEach(id => {
        const el = document.getElementById(`content-${id}`);
        if (el) el.classList.add('hidden');
        const link = document.getElementById(`tab-${id}`);
        if (link) {
            link.classList.remove('border-brand-blueDark', 'text-brand-blueDark');
            link.classList.add('border-transparent', 'text-slate-500');
        }
    });

    const selectedContent = document.getElementById(`content-${tabName}`);
    if (selectedContent) selectedContent.classList.remove('hidden');
    
    const selectedLink = document.getElementById(`tab-${tabName}`);
    if (selectedLink) {
        selectedLink.classList.remove('border-transparent', 'text-slate-500');
        selectedLink.classList.add('border-brand-blueDark', 'text-brand-blueDark');
    }

    // Lógica específica por tab (ex: carregar anexos)
    if (tabName === 'anexos') {
        loadAnexos();
    }
}
```

---

## Boas Práticas

### 1. Sempre Use Placeholders

Todos os campos de texto devem ter placeholders informativos:

```html
<input type="text" class="form-input" placeholder="Digite o part number">
<textarea class="form-textarea" rows="3" placeholder="Descreva a peça"></textarea>
```

### 2. Campos Readonly

Campos readonly devem ter estilo visual diferenciado:

```html
<input type="text" class="form-input" readonly>
```

CSS já aplicado:
```css
.form-input[readonly] {
    background-color: #F1F5F9;
    cursor: not-allowed;
}
```

### 3. Estados de Loading

Sempre mostre feedback visual durante operações assíncronas:

```javascript
btn.innerHTML = `<i class="ph ph-spinner animate-spin"></i> Salvando...`;
```

### 4. Tratamento de Erros

Sempre use try/catch e console.error para erros:

```javascript
try {
    // operação
} catch (error) {
    console.error("Erro ao executar operação:", error);
    // feedback ao usuário
}
```

### 5. Validação de componentData

Sempre valide se componentData está disponível antes de usar:

```javascript
if (!componentData.queryData || componentData.queryData.trim() === '') {
    console.error("Query não configurada.");
    return;
}
```

### 6. IDs Consistentes

Use padrão consistente para IDs:
- Inputs: `input-{nome-campo}`
- Selects: `select-{nome-campo}`
- Tabs: `tab-{nome-tab}` e `content-{nome-tab}`
- Botões: `btn-{acao}` ou `save-button`, `btn-aprovar`

### 7. Classes CSS Reutilizáveis

Use as classes CSS definidas no componente:
- `.form-label` - Labels de campos
- `.form-input` - Inputs de texto
- `.form-select` - Selects
- `.form-textarea` - Textareas
- `.custom-checkbox` - Checkboxes
- `.required-field` - Campos obrigatórios
- `.required-label` - Labels de campos obrigatórios

### 8. Responsividade

Sempre considere mobile:
- Use `grid-cols-1 md:grid-cols-2` para grids
- Sidebar com `hidden lg:block` e toggle button no header
- Overlay escuro quando sidebar aberta em mobile
- Tabs funcionam bem em mobile
- Inputs com largura total em mobile
- Transições suaves para abertura/fechamento de sidebar

### 9. Botões de Ação no Header

**⚠️ REGRA OBRIGATÓRIA:** Todos os botões de ação devem estar no **header**, nunca no footer.

**Estrutura:**
```html
<header class="...">
    <div class="flex items-center justify-between ...">
        <!-- Lado esquerdo: toggle sidebar + badge -->
        <div class="flex items-center gap-4">
            <!-- Conteúdo esquerdo -->
        </div>
        
        <!-- Lado direito: Botões de ação -->
        <div class="flex items-center gap-3">
            <button id="save-button" onclick="saveData()" class="...">
                <i class="ph ph-floppy-disk"></i> Salvar
            </button>
            <button id="btn-aprovar" onclick="aprovarCard()" class="...">
                Aprovar para Em Análise (Eng)
                <i class="ph ph-arrow-right"></i>
            </button>
        </div>
    </div>
</header>
```

**Padrões de Botões:**
- **Botão Primário (Salvar):** `bg-brand-orange hover:bg-brand-orangeDark`
- **Botão Secundário (Ações):** `bg-brand-blueDark hover:bg-[#001e40]`
- **Espaçamento:** `gap-3` entre botões
- **Tamanho:** `text-sm font-semibold py-2 px-5`
- **Ícones:** Sempre incluir ícone relevante

**Estados:**
- **Desabilitado:** Adicionar classe `btn-disabled` e atributo `disabled`
- **Loading:** Substituir conteúdo por spinner: `<i class="ph ph-spinner animate-spin"></i> Processando...`

### 10. Hierarquia Visual

Para reforçar a hierarquia entre elementos:

**Header:**
- Sombra mais pronunciada: `shadow-md` em vez de `shadow-sm`
- Borda mais destacada: `border-b-2` em vez de `border-b`

**Sidebar:**
- Gradiente de fundo para diferenciação
- Borda direita destacada: `border-r-2 border-brand-blueDark`
- Cards internos para organização visual

**Área Principal:**
- Header de contexto para indicar "itens filhos"
- Cards agrupados para seções relacionadas
- Divisores mais claros: `border-t-2` em vez de `border-t`

**Header:**
- Todos os botões de ação devem estar no header
- Botões agrupados com `gap-3` para espaçamento consistente
- Não usar footer para botões de ação
- Sombra mais pronunciada: `shadow-md` em vez de `shadow-sm`
- Borda mais destacada: `border-b-2` em vez de `border-b`

---

## Checklist para Novos Componentes

Ao criar um novo componente de etapa, verifique:

- [ ] Cores da marca aplicadas corretamente (#002855 e #FF6600)
- [ ] Fonte Inter carregada via Google Fonts
- [ ] Campos obrigatórios formatados (asterisco vermelho + borda esquerda)
- [ ] **Sidebar com destaque visual** (gradiente, borda destacada, cards internos)
- [ ] **Header de contexto** na área principal indicando "Itens Filhos"
- [ ] **Formulários organizados em cards/seções** quando apropriado
- [ ] **Ícones nas seções principais** do formulário
- [ ] **Hierarquia visual reforçada** (bordas mais espessas, sombras adequadas)
- [ ] Placeholders em todos os campos de texto
- [ ] `queryMitra` usado corretamente (string direta, não objeto)
- [ ] Função `mapQueryToObject` usada para converter resultados
- [ ] Tratamento de erros com try/catch e console.error
- [ ] Validação de componentData antes de usar
- [ ] Estados de loading implementados
- [ ] **Responsividade testada** (sidebar toggle em mobile, overlay)
- [ ] **Botões de ação no header** (não usar footer)
- [ ] IDs consistentes e descritivos
- [ ] Espaçamento consistente (space-y-6, gap-6)
- [ ] Bordas e sombras aplicadas corretamente

---

## Referências

- **Design System Base:** `components/crm/cliente_tencnofibras/board.html`
- **Componente de Referência:** `components/crm/cliente_tencnofibras/1_etapa_comercial.html`
- **Documentação Mitra:** `.cursorrules` (seção sobre funções nativas)

---

## Notas Importantes

1. **Nunca altere funcionalidades Mitra:** Todas as funções `queryMitra`, `setVariableMitra`, `dbactionMitra`, etc. devem funcionar exatamente como documentado.

2. **Preserve IDs:** IDs de elementos são referenciados no JavaScript - não altere sem atualizar o código JS.

3. **ComponentData retorna strings:** Sempre converta valores de `componentData` para o tipo correto (parseInt, JSON.parse, etc.).

4. **Navegação via iframes:** Nunca use métodos tradicionais de navegação - sempre use funções Mitra (`modalMitra`, `actionMitra`).

---

## Padrões de Design Visual - Hierarquia e Organização

### Princípios de Hierarquia Visual

O design deve comunicar claramente a relação entre elementos:

1. **Sidebar = Cabeçalho do Item Kanban**
   - Deve ter destaque visual claro
   - Fundo diferenciado (gradiente)
   - Borda destacada (border-r-2)
   - Cards internos para organização

2. **Área Principal = Itens Filhos**
   - Header de contexto indicando "Itens Filhos"
   - Background branco contrastando com sidebar
   - Formulários organizados em seções visuais

3. **Separação Visual Clara**
   - Bordas mais espessas (`border-2`) para elementos principais
   - Sombras adequadas (`shadow-md` para header, `shadow-lg` para sidebar)
   - Espaçamento generoso entre seções

### Padrão de Cards para Agrupamento

Use cards para agrupar campos relacionados:

```html
<!-- Estrutura de Card -->
<div class="bg-slate-50 rounded-xl p-6 border border-slate-200 mb-6">
    <!-- Cabeçalho do Card com Ícone -->
    <div class="flex items-center gap-2 mb-4">
        <i class="ph ph-{icon-name} text-brand-blueDark text-lg"></i>
        <h3 class="text-base font-bold text-slate-800">Título da Seção</h3>
    </div>
    <!-- Conteúdo do Card -->
    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
        <!-- Campos aqui -->
    </div>
</div>
```

### Padrão de Seções com Divisores

Para seções longas (como na aba Formulário):

```html
<section class="border-t-2 border-slate-200 pt-8 mt-8">
    <!-- Cabeçalho com Ícone -->
    <div class="flex items-center gap-2 mb-6 pb-3 border-b-2 border-slate-200">
        <i class="ph ph-package text-brand-blueDark text-xl"></i>
        <h3 class="text-lg font-bold text-slate-800">Título da Seção</h3>
    </div>
    <!-- Conteúdo -->
</section>
```

**Diferenças:**
- `border-t-2` e `border-b-2` (mais espessos) em vez de `border-t` e `border-b`
- Ícone `text-xl` (maior) para seções principais
- Espaçamento `pt-8 mt-8` entre seções

---

**Última atualização:** Dezembro 2024  
**Versão:** 2.0 - Atualizado com padrões de hierarquia visual e organização em cards
