# Party2Go Full Stack Logistics

**Party2Go** é uma plataforma digital integrada para organização e gestão de eventos infantis. Resolve o problema de famílias e empresas que têm dificuldade em encontrar, comparar e encomendar serviços de festas (insufláveis, catering, animadores, etc.) de múltiplos fornecedores de forma centralizada. A plataforma centraliza esta procura e oferece um sistema de logística próprio para garantir entregas atempadas e acompanhamento em tempo real.

## 📂 Estrutura de Ficheiros

O projeto utiliza uma arquitetura de monorepo dividida em serviços independentes:

- `backend/`: API REST e servidor WebSocket construídos com Strapi v5. Gere a base de dados, lógica de negócio e eventos em tempo real.
- `frontoffice/`: Portal do cliente (Porta 5175). Interface pública para browsing de produtos, criação de packs de festa, checkout, perfil pessoal do cliente e tracking de encomendas.
- `backoffice/`: Dashboard de administração (Porta 5174). Sistema para operadores gerirem a logística, KPIs, mapa em tempo real, gestão de frota e entregas.
- `pwa/`: Aplicação Mobile para Estafetas (Porta 5173). Progressive Web App (PWA) com suporte offline, navegação de rotas e entrega do pedido.
- `shared/`: Código partilhado entre os frontends (componentes Vue, composables de tracking, stores Pinia e utilitários de API).

## ✨ Funcionalidades

- **Tracking em Tempo Real:** Acompanhamento instantâneo da localização das entregas via Socket.io e Mapbox.
- **Múltiplos Perfis de Acesso:** Sistemas adaptados com permissões granulares para Administradores, Estafetas, Clientes Particulares e Clientes Empresariais, com login via Firebase.
- **Criação de Packs Customizados:** Carrinho de compras avançado que permite a composição flexível de "Packs de Festa".
- **Carrinho e Checkout** - gestão de carrinho, com modo cliente particular e empresa
- **Gestão de Frota e Logística:** Dashboard com KPIs em tempo real e visualização global no mapa de todos os estafetas e encomendas ativas.
- **Progressive Web App (PWA):** App dedicada aos estafetas com rota otimizada, atualizações de GPS em background e prova de entrega fotográfica.
- **Chat Bidirecional:** Comunicação em tempo real entre os clientes e os estafetas durante a entrega, assim como o estafeta com o administrador.
- **Prova de Entrega** - captura de foto/assinatura no momento da entrega
- **Recuperação de Password** - sistema próprio de tokens com expiração, enviado por email

## 👤 Perfis de Utilizador
 
| Role | Aplicação de acesso | Permissões principais |
|---|---|---|
| **admin** | Backoffice | Gestão de frota, atribuição de entregas, dashboard de KPIs, visualização de mapa geral |
| **estafeta** | PWA | Ver entregas do dia, navegação, chat com clientes, submissão de prova de entrega |
| **business** | Frontoffice (modo empresa) | Encomendas em nome de empresa, área de cliente empresarial, tracking dedicado |
| **client** (particular) | Frontoffice | Criar packs de festa, checkout, tracking da própria encomenda, chat com o estafeta |
 
> O redirecionamento pós-login é feito por regras de email/Firestore (`getRedirectPath`), que determinam para qual aplicação e rota o utilizador é enviado.
 
## 🔌 Principais Content-Types (API)
 
O backend expõe 11 domínios via REST API do Strapi, com destaque para:
 
| Content-Type | Descrição |
|---|---|
| `order` | Encomenda: entidade central, com estado (`pending → aceite → transito → concluida`), histórico de localização e referências a cliente/estafeta |
| `product` | Produtos individuais (insufláveis, catering, etc.) |
| `combo` | Pacotes pré-configurados de produtos |
| `courier` | Dados dos estafetas, incluindo localização atual e estado (ativo/inativo) |
| `client` | Clientes particulares e empresas |
| `message` | Mensagens de chat entre cliente e estafeta |
| `delivery-proof` | Prova de entrega (foto/assinatura) |
| `vehicle-id` | Tipos de veículos |
| `password-reset-token` | Tokens de recuperação de password (tabela customizada) |
 
Exemplo de acesso: `GET /api/products?populate=*`
 

## 🛠️ Tecnologias

**Backend:**
- Node.js (v20+)
- Strapi v5 (API REST & CMS)
- PostgreSQL (Base de dados primária)
- Socket.io (WebSockets para tempo real)
- Firebase Admin SDK (Autenticação)
- Nodemailer

**Frontend (Frontoffice, Backoffice, PWA):**
- Vue.js 3 (Composition API)
- Vite (Build tool)
- Vue Router
- Pinia (Gestão de estado)
- Bootstrap 5 & Bootstrap Icons
- Socket.io Client
- Mapbox GL / Leaflet (Mapas e Geocoding)
- Firebase Auth

## 🚀 Como Executar o Projeto

### Pré-requisitos
- [Node.js](https://nodejs.org/) v20 a v24
- [PostgreSQL](https://www.postgresql.org/) 14+ (ou usar SQLite localmente)
- Conta [Firebase](https://firebase.google.com/) configurada (Auth + Firestore)
- Token de acesso [Mapbox](https://www.mapbox.com/)

### Passos de Instalação

1. **Clonar o repositório:**
   ```bash
   git clone <repo-url>
   cd H103
   ```

2. **Iniciar o Backend:**
   ```bash
   cd backend
   npm install
   # Criar o ficheiro .env com as variáveis de ambiente (DATABASE, FIREBASE, MAPBOX)
   npm run dev
   # O backend ficará disponível em http://localhost:1337
   ```

3. **Iniciar o Frontoffice (Portal do Cliente):**
   Num novo terminal:
   ```bash
   cd frontoffice
   npm install
   npm run dev
   # Disponível em http://localhost:5175
   ```

4. **Iniciar o Backoffice (Admin Dashboard):**
   Num novo terminal:
   ```bash
   cd backoffice
   npm install
   npm run dev
   # Disponível em http://localhost:5174
   ```

5. **Iniciar a PWA (App Estafetas):**
   Num novo terminal:
   ```bash
   cd pwa
   npm install
   npm run dev
   # Disponível em http://localhost:5173
   ```
> São necessários **4 terminais** em simultâneo (backend + 3 frontends), já que o projeto ainda não usa `concurrently` nem workspaces.

## 📈 Como pode ser melhorado

- [ ] **Segurança e Variáveis de Ambiente:** Migrar as credenciais do Firebase que estão fixas no código para ficheiros `.env` e implementar HTTPS.
- [ ] **Infraestrutura e Deploy:** Implementar contentorização com Docker e Docker Compose, e estabelecer pipelines de CI/CD (ex: GitHub Actions).
- [ ] **Testes Automatizados:** Adicionar testes unitários com Jest/Vitest e testes End-to-End (E2E) com Cypress ou Playwright.
- [ ] **Performance e Escalabilidade:** Introduzir Redis para caching de encomendas e estafetas, e otimizar queries pesadas.
- [ ] **Qualidade de Código:** Configurar e aplicar linting e formatação automática em todos os repositórios (ESLint + Prettier).

## 👥 Autores
- [Rodrigo Santiago Fonseca Faria Abreu](https://github.com/rodrigoo-abreu)
- [Afonso Martim Carvalho Leite](https://github.com/afonsooleite)
- [Filipa Mendes de Castro Pinto](https://github.com/filipamcp)
- [Pedro Manuel Mendes Neves](https://github.com/pedro2516)
- [Sérgio Paulo Vieira Carvalho](https://github.com/serginho355)
