---
description: Project memory — decisions, blockers, todos, lessons carried across sessions.
load: eager
---

# Project State

> Last updated: 2026-06-21

## 🎯 Current Focus

Módulo 2 — Deploys, previews, ambientes e rollback (~3h).
Objetivo: preview por branch para revisão do cliente, rollback, variáveis de ambiente por ambiente.

## ✅ Decisions

- **2026-06-21 — Docker-first dev**: app roda exclusivamente no container. `node_modules` isolado em volume anônimo. Para adicionar deps: `npm install <pkg> --package-lock-only` → `docker compose build` → `docker compose up`.
- **2026-06-21 — Wrangler global**: instalado no host (`npm install -g wrangler`), autenticado via `wrangler login`. Não é dependência do projeto.
- **2026-06-21 — Cloudflare Pages**: projeto `my-landing-page-3iz`, produção em `https://my-landing-page-3iz.pages.dev`. Integração Git ativa — push na `main` dispara deploy automático.
- **2026-06-21 — SSH multi-conta**: `~/.ssh/config` com `IdentitiesOnly yes` no bloco `github.com-pessoal`. Remotes de projetos pessoais usam `git@github.com-pessoal:<user>/<repo>.git`.

## 📋 Todos

- [ ] Iniciar Módulo 2: criar branch de exemplo, obter URL de preview, simular envio ao cliente
- [ ] Praticar rollback via painel Cloudflare
- [ ] Configurar variáveis de ambiente por ambiente (produção vs preview)

## 📝 Lessons Learned

- Build remoto da Cloudflare lê `.tool-versions` (asdf) E `.nvmrc` — `.nvmrc` vence para Node. Manter os dois alinhados conceitualmente mesmo com versões diferentes.
- `@rollup/rollup-linux-x64-musl` é binário nativo Linux Alpine — ausente quando o `package-lock.json` é gerado no macOS. `docker compose exec dev npm install` resolve dentro do container.
- Cloudflare deduplica assets por hash — segundo deploy (via Git) foi instantâneo porque os arquivos já estavam na CDN do Direct Upload.
