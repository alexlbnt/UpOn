# CLAUDE.md — UPON

Guia para sessões do Claude Code neste repositório. Leia antes de mudar qualquer coisa.

## Produto e arquitetura (resumo)

UPON é uma plataforma de cupons de desconto locais: estabelecimentos publicam ofertas, consumidores resgatam cupons para uma carteira no app e os usam no caixa com um **QR rotativo (TOTP)**, validado online pelo funcionário no **modo lojista** do mesmo app.

- **App** Expo (React Native) para consumidor e lojista · **Painel** React + Vite (SPA) para estabelecimentos e admin da plataforma · **API** NestJS (Fastify) como monólito modular, com processos `api` e `worker` na mesma imagem Docker.
- **Dados:** PostgreSQL + PostGIS (Supabase, só como Postgres) via Prisma · **Redis + BullMQ** para filas, cache, rate limit e idempotência · **Cloudflare** (DNS, WAF, CDN, Pages, R2).

Fonte da verdade: [`docs/PLANEJAMENTO_UPON.md`](docs/PLANEJAMENTO_UPON.md) (requisitos, arquitetura, plano) e [`docs/adr/`](docs/adr/) (decisões). A tabela "Decisões já tomadas" do planejamento **não** é reaberta sem motivo novo; se algo do plano estiver errado ou impossível, **pare e pergunte** antes de desviar.

## Repositório

- Remoto: **github.com/alexlbnt/UpOn**, branch principal `main`.
- O repositório antigo (`alexlbnt/App_Upon`) **não** é base de código; só consulta pontual se o Alexandre pedir.

## Estrutura do monorepo

pnpm workspaces + Turborepo (ADR-001).

| Caminho | Papel |
|---|---|
| `apps/api` | NestJS + Fastify. Entradas `main.ts` (HTTP) e `worker.ts` (BullMQ + cron). Módulos em `src/modules/<módulo>/{http,application,domain,infra}` |
| `apps/mobile` | Expo Router. Grupos `(public)`, `(auth)`, `(consumer)`, `(merchant)`; código em `src/features/*` |
| `apps/admin` | Painel React + Vite, TanStack Router/Query, shadcn/ui |
| `packages/contracts` | **Única fonte** de schemas Zod, enums, tipos e códigos de erro |
| `packages/api-client` | Cliente + hooks TanStack Query **gerados** pelo Orval a partir do `openapi.json` (não editar à mão) |
| `packages/domain-rules` | Funções puras compartilhadas (TOTP, cálculo de status, formatação de desconto) |
| `packages/design-tokens` | Cores, espaçamentos e tipografia (Tailwind no painel, NativeWind no app) |
| `packages/config` | tsconfig, ESLint, Prettier e Vitest base |
| `infra/docker` | Dockerfile da API e `docker-compose.yml` de dev (PostGIS, Redis, MinIO) |
| `docs/` | Planejamento, ADRs e `api/openapi.json` (artefato versionado) |

## Regras de arquitetura inegociáveis

1. **Regra de negócio só em `domain/` do backend** (`apps/api/src/modules/*/domain`): entidades, máquinas de estado, políticas e erros de domínio, **sem** Nest e **sem** Prisma, testadas sem banco.
2. **Contratos só em `packages/contracts`.** Nenhum schema, enum ou código de erro duplicado em app, painel ou API.
3. **Nada de regra de negócio no front** (app e painel). O front valida formulário com os schemas do contrato e esconde botões; quem decide é a API.
4. **Autorização sempre na API**, em toda rota: guard JWT global (`@Public()` nas abertas), guard de papel da plataforma e políticas de domínio consultando o banco (papéis de lojista nunca vêm do token).
5. **Dinheiro em centavos inteiros** (`*Cents`); formatação só na exibição.
6. **Datas em UTC** (`timestamptz`, ISO 8601); `America/Sao_Paulo` só na exibição.
7. **Supabase só como Postgres**: nada de Supabase Auth, Storage, RLS, Realtime ou Edge Functions; só SQL/PostGIS padrão.
8. Ids UUID v7; distância nunca gravada; invariantes críticas com `UNIQUE`/`CHECK` no banco (ADR-012).
9. Segredos só em variáveis de ambiente; `.env*` nunca versionado (só `.env.example`); tokens e senhas nunca em log.
10. Dependências do app sempre com `npx expo install` (versões alinhadas ao SDK).

## Comandos principais

> Disponíveis à medida que a Fase 0 é concluída. Requisitos: Node da versão em `.nvmrc`, pnpm (via Corepack) e Docker.

| Ação | Comando |
|---|---|
| Instalar | `corepack enable && pnpm install` |
| Subir a infraestrutura local | `docker compose -f infra/docker/docker-compose.yml up -d` |
| Rodar tudo em dev (API, painel, app) | `pnpm dev` |
| Lint / tipos / testes / build | `pnpm lint` · `pnpm typecheck` · `pnpm test` · `pnpm build` |
| Gerar `openapi.json` e o cliente da API | `pnpm api:generate` |
| Nova migration (dev) | `pnpm --filter @upon/api prisma migrate dev --name <nome>` |
| Aplicar migrations (staging/prod) | `pnpm --filter @upon/api prisma migrate deploy` |
| Seed fictício | `pnpm --filter @upon/api db:seed` |
| Checar versões do Expo | `pnpm --filter @upon/mobile exec expo install --check` |

## Padrões de trabalho (planejamento §4.3)

- **Branches:** trunk-based; `main` sempre publicável; branches curtas `feat/…`, `fix/…`, `chore/…`, `docs/…`.
- **Commits:** Conventional Commits validados pelo commitlint, escopo = app ou pacote (`feat(api): …`, `fix(mobile): …`, `chore(repo): …`, `docs(adr): …`).
- **PRs:** pequenos (idealmente < 400 linhas), template com contexto, como testar, prints e checklist (testes, migrations, impacto no contrato); **squash merge**; CI verde obrigatório; revisão por outra pessoa ou revisão com IA + autorrevisão.
- **Migrations:** geradas pelo Prisma e revisadas; nunca editar migration aplicada; mudanças destrutivas em dois passos (expandir → migrar → contrair).
- **Versões:** SemVer com tags `vX.Y.Z`; CHANGELOG gerado dos commits.
- **Nunca** `git push --force` na `main`. Não criar contas nem recursos pagos (Supabase, Railway, Cloudflare, EAS) sem confirmação do Alexandre.

**Definição de pronto:** critérios de aceite atendidos · testes · lint e tipos ok · OpenAPI atualizado · deploy em staging · sem erro novo no Sentry.

## Status atual

- **Fase:** Fase 0 — Fundação (em andamento).
- **Concluído:** documentação inicial (planejamento, ADRs 001–012, este arquivo, README).
- **Próximo:** base do monorepo → docker-compose → contracts → API → api-client → mobile → admin → CI.
- **Pendente fora do código:** staging (Supabase, Railway, Cloudflare Pages/R2, EAS `preview`) e proteção da `main` no GitHub, a cargo do Alexandre.
- **Fluxo atual:** push direto na `main` permitido só durante a fundação; PR obrigatório depois que o CI estiver ativo.
