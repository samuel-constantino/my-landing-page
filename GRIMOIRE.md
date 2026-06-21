# 🧙‍♂️ Grimoire — my-landing-page

Memória central do projeto — decisões, arquitetura, convenções. Lido por qualquer agente que trabalhar aqui.

## 🌟 Visão Geral

Projeto de **prática de provisionamento** de uma aplicação na **Cloudflare**.

Veículo da prática: a **primeira landing page freelance** do dono do projeto — página comercial para um **cliente real pagante** (lead em andamento, **ainda não fechado** formalmente). Objetivo ponta a ponta: **desenvolvimento → provisionamento → entrega/handoff ao cliente**.

Dedicação: ~10–15h/semana. Roadmap de aprendizado Cloudflare Pages com **9 módulos**; estágio atual: **Módulo 2 — Deploys, previews, ambientes e rollback**.

## 🏗️ Arquitetura

- **Framework:** Astro 6, **output estático** (`output: 'static'`), **sem adapter** neste momento (`astro.config.mjs` vazio = estático).
- **Estilo:** Tailwind CSS (a adicionar mais adiante).
- **Hospedagem:** Cloudflare **Pages** (decisão consciente — ver Notas).
- **Build:** `npm run build` → gera `dist/` (o que o Pages serve). `npm run preview` simula produção. Dev server em `http://localhost:4321`.
- **Modelo mental do deploy:** um *deployment* é um snapshot imutável de `dist/` servido na borda. Dois caminhos a praticar:
  1. **Direct Upload (CLI):** `wrangler pages deploy dist --project-name minha-landing`
  2. **Integração Git:** push na `main` dispara build + deploy automático.
- **Formulário de contato (futuro):** **indefinido** — Pages Functions OU serviço externo. Decisão depende do discovery com o cliente. ⚠️ **Não decidir sozinho.**
- **Adapter `@astrojs/cloudflare`:** só entra no **Módulo 5** (Functions/SSR). Não usar no Módulo 1.

## 🛠️ Componentes Chave

- **Local do projeto (decidido):** o projeto Astro **é a raiz de `/workspace`** — bind-mount persistente do host `/home/samuel/code/constantino/my-landing-page`. `GRIMOIRE.md` + `.agentic/` + `taskfile.yml` convivem na raiz e são **versionados** (disponíveis para outros devs). Limpeza do entregável para o cliente, se necessária, será tratada no handoff (filtro/export), não por diretório separado. Ver lição de persistência nas Notas.
- `.nvmrc` com `22` na raiz — **Astro 6 exige Node ≥ 22.12**; o build remoto da Cloudflare lê `.nvmrc`. Sem ele, o build pode falhar usando Node antigo. (Ambiente sandbox já tem Node v24.14.1, que satisfaz o piso.)
- Build settings no painel Cloudflare (preset Astro autopreenche): build command `npm run build`, output dir `dist`.
- **Nome do projeto Cloudflare:** `my-landing-page-3iz` (sufixo adicionado pela Cloudflare). URL de produção: `https://my-landing-page-3iz.pages.dev`. Módulo 3 cobre DNS com domínio customizado.
- ⚠️ **Domínio do cliente:** pendente — depende do discovery. Será configurado no Módulo 3.

## 🔧 Stack e Convenções

- **Ambiente do dono:** Linux + Zsh, Node via **asdf** (não nvm). `.tool-versions` na raiz pina Node 23.11.0 para o host; `.nvmrc` pina Node 22 para o build remoto da Cloudflare.
- **Docker:** fluxo Docker-first para dev local. `compose.yml` com volume anônimo para `node_modules`. App roda exclusivamente no container; host só tem `node_modules` para editor (VS Code). Para adicionar deps: `npm install <pkg> --package-lock-only` no host → `docker compose build` → `docker compose up`.
- **Ferramentas Cloudflare:** Wrangler CLI instalado globalmente no host (`npm install -g wrangler`). Autenticado via `wrangler login`.
- **Sensores:** ver `taskfile.yml` (contrato `task lint/typecheck/test/validate`).

### Como o dono gosta de trabalhar (crítico)

- Dev **sênior em consolidação**. Quer **profundidade técnica e o "porquê"** de cada passo não-óbvio — não só o comando pronto.
- 🔒 **Regra inviolável:** **não inferir nem deduzir escopo ambíguo. Perguntar antes de assumir.** Se há mais de um caminho razoável, consultar. **Uma pergunta de cada vez.** (Esta regra sobrepõe a diretriz global de "agir com iniciativa".)

## 📝 Notas e Lições Aprendidas

### Decisão Cloudflare: Pages hoje, Workers depois (NÃO "corrigir")

- Em 2026 o **Pages está em modo de manutenção**; **Workers Static Assets** é o sucessor e recebe o investimento novo da Cloudflare.
- Para uma **landing estática**, o **Pages ainda é a opção mais simples e suficiente AGORA**.
- Decisão consciente: **Pages hoje**, mantendo conhecimento **transferível** para Workers Static Assets (há módulo de migração no roadmap). **Não empurrar reescrita em Workers ainda.** Não tratar isso como desatualização.

### Persistência no sandbox-ai (lição) — o projeto vive em `/workspace`

- Cada `sandbox-ai claude` sobe um **container novo**; só persiste o que é **bind-mount do host**: `/workspace` (o projeto) e alguns paths em `/home/sandbox` (`.ssh`, `.claude`, `.claude.json`, `.config/user`). Verificável via `/proc/mounts`.
- Qualquer arquivo fora desses mounts (ex.: `/home/sandbox/<qualquer-coisa>`) é **efêmero** e some no próximo run. Por isso o projeto e seus arquivos agênticos ficam em `/workspace`, nunca em diretório separado fora do mount.
- Telemetria do Astro: build local exige `ASTRO_TELEMETRY_DISABLED=1` (o sandbox não permite criar `/home/sandbox/.config/astro`). Quirk local — não afeta o build remoto da Cloudflare.

### Ações que dependem do humano (agente não faz sozinho)

- `wrangler login` (OAuth no navegador).
- Criar/autorizar repositório no **GitHub**.
- Conectar repo no **painel da Cloudflare** (selecionar conta/projeto).
- Fornecer dados do cliente/domínio no discovery (ainda não temos).

### Roadmap completo — 9 módulos

| # | Tema | Carga | Status |
|---|------|-------|--------|
| 1 | Fundamentos e primeiro deploy | ~3–4h | ✅ Concluído |
| 2 | Deploys, previews, ambientes e rollback | ~3h | 🔄 Atual |
| 3 | Domínios, DNS e TLS | ~4h | — |
| 4 | Configuração estática avançada | ~3h | — |
| 5 | Pages Functions (parte dinâmica) | ~5h | — |
| 6 | Dados no edge: KV, R2, D1 | ~4h | — |
| 7 | Observabilidade, analytics e segurança | ~3h | — |
| 8 | CI/CD avançado com Wrangler | ~3h | — |
| 9 | Convergência para Workers Static Assets | ~4h | — |

### Definição de "feito" — Módulo 1 ✅

Site Astro estático no ar em `https://my-landing-page-3iz.pages.dev`, deployado pelos **dois** caminhos (Direct Upload e integração Git), com `.nvmrc` pinado e **build remoto verde**.

### Módulo 2 — foco atual

Preview deployments por branch (link de revisão ao cliente sem tocar produção) + rollback instantâneo + variáveis de ambiente por ambiente.

### Módulo 3 — DNS e domínio customizado

Conectar domínio do cliente, configurar DNS na Cloudflare (proxied vs DNS-only), TLS automático, HTTPS forçado, redirect www → apex.
