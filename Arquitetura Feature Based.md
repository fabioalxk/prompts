# Prompt para Migração: Feature-Based Architecture

## INSTRUÇÃO PRINCIPAL

Migre este projeto React para uma arquitetura feature-based moderna, seguindo exatamente a estrutura e padrões descritos abaixo. Analise o código existente e reorganize conforme as regras estabelecidas.

## ESTRUTURA DE DIRETÓRIOS OBRIGATÓRIA

```
src/
├── pages/                     # Páginas que consomem features
│   ├── [NomePage].jsx
│   └── index.js              # Barrel export de todas as páginas
├── features/                  # Lógica de negócio por domínio
│   ├── [feature-name]/       # Nome em kebab-case
│   │   ├── components/       # Componentes específicos da feature
│   │   │   ├── [Component].jsx
│   │   │   └── index.js      # Barrel export
│   │   ├── hooks/           # Hooks específicos da feature
│   │   │   ├── use[Hook].js
│   │   │   └── index.js
│   │   ├── services/        # APIs e lógica de dados
│   │   │   ├── [service]API.js
│   │   │   └── index.js
│   │   ├── types/           # TypeScript types (se aplicável)
│   │   │   └── [feature].types.ts
│   │   ├── utils/           # Utilitários específicos
│   │   └── index.js         # Barrel export da feature completa
│   └── index.js             # Barrel export de todas as features
├── shared/                   # Recursos compartilhados
│   ├── components/          # Componentes reutilizáveis
│   │   ├── [Component].jsx
│   │   └── index.js
│   ├── hooks/              # Hooks globais
│   │   └── index.js
│   ├── utils/              # Utilitários globais
│   │   └── index.js
│   ├── constants/          # Constantes globais
│   └── types/              # Types globais (se TypeScript)
├── router/                  # Configuração de rotas
│   ├── AppRouter.jsx       # Configuração principal do router
│   ├── ProtectedRoute.jsx  # Componente para rotas protegidas
│   └── routes.js           # Constantes de rotas
└── App.jsx                 # Componente raiz
```

## REGRAS DE CLASSIFICAÇÃO

### O QUE VAI PARA PAGES/

- Componentes que representam uma rota específica
- Fazem composição de features para criar uma tela
- Nome sempre termina com "Page" (ex: LoginPage, HomePage)
- Não contêm lógica de negócio, apenas composição
- Cada página consome uma ou mais features

### O QUE VAI PARA FEATURES/

- Lógica de negócio específica de um domínio
- Componentes que implementam funcionalidades específicas
- Hooks que gerenciam estado de uma funcionalidade
- Services que fazem chamadas de API relacionadas
- Nome da feature em kebab-case (ex: user-management, product-catalog)

### O QUE VAI PARA SHARED/

- Componentes reutilizados em 3+ features diferentes
- Hooks utilitários globais
- Utilitários que não pertencem a domínio específico
- Constantes globais da aplicação

## PADRÕES DE NOMENCLATURA

### Arquivos e Pastas

- **Features**: kebab-case (user-management, order-processing)
- **Componentes**: PascalCase (LoginForm.jsx, UserProfile.jsx)
- **Hooks**: camelCase começando com "use" (useAuth.js, useLocalStorage.js)
- **Services**: camelCase terminando com "API" (userAPI.js, orderAPI.js)
- **Pages**: PascalCase terminando com "Page" (LoginPage.jsx, DashboardPage.jsx)

### Exports

- Sempre usar barrel exports (index.js) em cada pasta
- Exports nomeados, não default exports nos barrels
- Pages podem usar default export

## IMPLEMENTAÇÃO DE BARREL EXPORTS

### Em cada feature (src/features/[feature]/index.js):

```js
// src/features/authentication/index.js
export { LoginForm, SignupForm } from "./components";
export { useAuth, useLogin } from "./hooks";
export { authAPI } from "./services";
```

### No index principal de features (src/features/index.js):

```js
// src/features/index.js
export * from "./authentication";
export * from "./user-management";
export * from "./dashboard";
```

### Em shared (src/shared/components/index.js):

```js
// src/shared/components/index.js
export { Button } from "./Button";
export { Modal } from "./Modal";
export { Layout } from "./Layout";
```

## ESTRUTURA DE PÁGINAS

### Padrão de implementação:

```jsx
// src/pages/[Nome]Page.jsx
import { Feature1Component, Feature2Component } from '../features'
import { Layout } from '../shared/components'

export default function [Nome]Page() {
  return (
    <Layout>
      <div className="page-container">
        <Feature1Component />
        <Feature2Component />
      </div>
    </Layout>
  )
}
```

## CONFIGURAÇÃO DE ROTAS

### src/router/routes.js:

```js
// src/router/routes.js
export const ROUTES = {
  HOME: "/",
  LOGIN: "/login",
  DASHBOARD: "/dashboard",
  // Adicione todas as rotas aqui
};
```

### src/router/AppRouter.jsx:

```jsx
// src/router/AppRouter.jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";
import { ROUTES } from "./routes";
// Import all pages
import HomePage from "../pages/HomePage";
import LoginPage from "../pages/LoginPage";

export default function AppRouter() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path={ROUTES.HOME} element={<HomePage />} />
        <Route path={ROUTES.LOGIN} element={<LoginPage />} />
        {/* Adicione todas as rotas */}
      </Routes>
    </BrowserRouter>
  );
}
```

## PROCESSO DE MIGRAÇÃO

### 1. ANÁLISE INICIAL

- Identifique todas as rotas/páginas existentes
- Mapeie componentes por funcionalidade/domínio
- Liste hooks, services e utilitários existentes

### 2. CRIAÇÃO DA ESTRUTURA

- Crie a estrutura de pastas conforme template
- Não mova arquivos ainda, apenas crie as pastas

### 3. IDENTIFICAÇÃO DE FEATURES

Para cada funcionalidade distinta, crie uma feature:

- **Autenticação**: login, signup, logout, recuperação de senha
- **Dashboard**: métricas, gráficos, resumos
- **Perfil de usuário**: edição, configurações, preferências
- **[Domínio específico]**: funcionalidades específicas do negócio

### 4. MIGRAÇÃO SISTEMÁTICA

Por feature:

1. Mova componentes relacionados para `features/[nome]/components/`
2. Mova hooks relacionados para `features/[nome]/hooks/`
3. Mova services para `features/[nome]/services/`
4. Crie barrel exports
5. Atualize imports

### 5. CRIAÇÃO DE PÁGINAS

- Extraia lógica de composição para páginas dedicadas
- Cada rota deve ter uma página correspondente
- Páginas apenas fazem composição, sem lógica

### 6. CONFIGURAÇÃO DE ROTAS

- Centralize todas as rotas em `router/routes.js`
- Configure AppRouter
- Mova lógica de proteção de rotas para ProtectedRoute

## CRITÉRIOS DE DECISÃO

### Quando criar nova feature:

- Funcionalidade tem 3+ componentes relacionados
- Possui lógica de negócio específica
- Pode ser desenvolvida/testada independentemente

### Quando colocar em shared:

- Usado em 3+ features diferentes
- Não possui lógica de negócio específica
- É utilitário/genérico

### Quando criar nova página:

- Cada rota única deve ter uma página
- Páginas fazem apenas composição de features

## VALIDAÇÃO FINAL

Após migração, verifique:

- [ ] Cada feature tem barrel export completo
- [ ] Páginas fazem apenas composição
- [ ] Rotas estão centralizadas em routes.js
- [ ] Não há imports diretos entre features
- [ ] Shared contém apenas código realmente compartilhado
- [ ] Todos os imports foram atualizados
- [ ] Aplicação funciona sem erros

## EXEMPLO DE MAPEAMENTO

Se você tem:

```
src/
├── components/
│   ├── LoginForm.js     → features/authentication/components/
│   ├── UserProfile.js   → features/user-management/components/
│   ├── Button.js        → shared/components/
└── pages/
    └── Login.js         → pages/LoginPage.jsx
```

Execute esta migração mantendo funcionalidade 100% equivalente, atualizando todos os imports e criando todos os barrel exports necessários.
