# 🛠️ Software Design Document (SDD)

**Projeto:** [Cadê Meu Amigo?]
**Versão:** 1.0.0  
**Status:** ⚪ Aguardando Geração de Especificações.

## 🤖 1. Orquestração e Contexto de IA (MCP)
> Configuração dos servidores Model Context Protocol para a IDE Agêntica.

* **Figma/Stitch MCP:** `[LINK DO ARQUIVO]` (Ler design tokens, cores e hierarquia visual).
* **Supabase MCP:** Contexto do banco de dados real e políticas de RLS.
* **GitHub MCP:** Leitura das Issues do Kanban para orientar a implementação (Spec-Driven).

## 📦 2. Stack Tecnológica e Bibliotecas
> Definição estrita das tecnologias permitidas (package.json). Nenhuma dependência externa deve ser instalada sem refletir aqui.

* **Core:** Angular 21+ (Standalone / Signals).
* **BaaS & Auth:** Supabase-js.
* **Estilização & UI:** Tailwind CSS, Spartan UI (HLM), Lucide Angular (Ícones).
* **Utilitários:** [Ex: date-fns para datas, zod para schemas].

## 🗄️ 3. Arquitetura de Dados

### 📖 3.1. Glossário Técnico (Mapeamento)
| Termo PRD (PT-BR) | Entidade Técnica (EN) | Atributos Principais |
| :--- | :--- | :--- |
| Ex: Sorteio | `draw` | `id`, `group_id`, `drawer_id` |

### 📊 3.2. Diagrama ER (Mermaid)
> [O Código do Diagrama Mermaid será inserido aqui no próximo passo]


## 📑 4. Contratos Globais (Interfaces & Types)
> Tipagem TypeScript baseada no banco de dados.

> [Interfaces TypeScript globais serão inseridas aqui]

## 🏗️ 5. Scaffolding Macro (Arquitetura Frontend)
### 📂 5.1. Estrutura de Pastas Global (Workspace)
O projeto utiliza uma estrutura de Monorepo para separar a documentação, o backend (futuro) e o frontend.

* **`docs/`**: Documentação oficial do projeto (PRD, SDD, manuais).
* **`apps/api/`**: Reservado para o Backend/Servidor (Node/Supabase Edge Functions).
* **`apps/web/`**: Aplicação Frontend principal (Angular + Tailwind).

---
🛑 **PARE AQUI POR ENQUANTO!** 🛑
> As seções abaixo exigem conhecimentos de Angular e Segurança que estudaremos mais adiante. Apenas deixe a estrutura copiada no seu arquivo para usarmos no futuro. Não se preocupe em preenchê-las agora!
---

### 🧩 5.2. Arquitetura Frontend (`apps/web/src/app/`)
Adotamos a arquitetura **Feature-Driven**, onde o código é organizado por domínios de negócio, promovendo o desacoplamento e a escalabilidade.

* **`core/`**: A "fundação" do app. Contém elementos que rodam apenas uma vez (Singletons), como Guards, Interceptors e Services globais (ex: `AuthService`).
* **`shared/`**: A "caixa de ferramentas". Contém componentes UI "burros" (botões, inputs, cards genéricos), Pipes e Diretivas reutilizáveis por qualquer feature.
* **`features/`**: O "coração" do negócio. Cada subpasta representa um domínio funcional completo.

### 🗺️ 5.3. Mapa de Domínios (Features Planejadas)
> **Instrução para a IA:** Os domínios abaixo devem ser criados fisicamente dentro de `src/app/features/` apenas no momento da implementação de suas respectivas User Stories.

| Domínio (Feature) | Responsabilidade Macro | Principais Rotas Relacionadas |
| :--- | :--- | :--- |
| `auth` | Gerenciar login, registro e sessão do usuário. | `/login`, `/register` |
| `sorteio` | Fluxo de criação e gerenciamento de grupos de sorteio. | `/sorteios`, `/novo-sorteio` |
| `participante` | Gerenciar perfil, lista de desejos e convites. | `/perfil`, `/meus-desejos` |
| `sorteio-resultado` | Visualização do resultado do sorteio (Quem eu tirei). | `/resultado/:id` |

### 🧠 5.4. Core Services (Singleton)
Serviços que mantêm o estado global da aplicação e se comunicam com o Supabase.

| Service | Localização Planejada | Responsabilidade |
| :--- | :--- | :--- |
| `AuthService` | `core/services/auth.service.ts` | Login, Logout e monitoramento do `authState`. |
| `ThemeService` | `core/services/theme.service.ts` | Controle de Dark Mode e Design Tokens dinâmicos. |

### 🚦 5.5. Mapa de Rotas e Contratos de Tela (Pages)
> **Instrução para a IA:** Esta tabela define a árvore de rotas (Routing) do Angular. A coluna "Parâmetros" dita os argumentos capturados pela rota, e a coluna "API / Supabase" dita o contrato de dados esperado para que a página funcione, servindo de base para a futura geração do Backend.

| Rota Frontend | Page Component (Caminho na Feature) | Parâmetros (Args) | Functional Guard | API / Ação Supabase Esperada |
| :--- | :--- | :--- | :--- | :--- |
| `/login` | `auth/pages/login/login.page.ts` | - | Público | `supabase.auth.signIn` |
| `/cadastro` | `auth/pages/register/register.page.ts` | - | Público | `supabase.auth.signUp` |
| `/sorteios` | `sorteio/pages/list/list.page.ts` | - | `AuthGuard` | `SELECT * FROM grupos_sorteio` |
| `/sorteio/:id` | `sorteio/pages/detail/detail.page.ts` | `id` (UUID) | `AuthGuard` | `SELECT * FROM grupos_sorteio WHERE id = :id` |
| `/convite/:hash`| `sorteio/pages/invite/invite.page.ts` | `hash` (String) | Público / `AuthGuard` | Inserção na tabela `participantes` |
| `/perfil` | `participante/pages/profile/profile.page.ts`| - | `AuthGuard` | `UPDATE participantes` |

## 🛡️ 6. Segurança (Supabase RLS)
> Políticas de acesso a nível de banco de dados.

| Tabela | Política (RLS) |
| :--- | :--- |
| `[tabela]` | [Regra de acesso] |