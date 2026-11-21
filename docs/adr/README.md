# Architecture Decision Records (ADRs)

Decisões de arquitetura do UPON. Origem: `docs/PLANEJAMENTO_UPON.md` §3.7.
Para mudar uma decisão aceita, crie um ADR novo que a substitua (status `substituído por ADR-NNN`) em vez de editar o antigo.

| ADR | Título | Status |
|---|---|---|
| [001](001-monorepo-pnpm-turborepo.md) | Monorepo com pnpm workspaces e Turborepo | aceito |
| [002](002-monolito-modular-em-container.md) | Monólito modular em container | aceito |
| [003](003-supabase-somente-postgres.md) | Supabase usado só como Postgres gerenciado | aceito |
| [004](004-prisma-typedsql.md) | Prisma com TypedSQL para PostGIS | aceito |
| [005](005-contratos-zod-openapi-cliente-gerado.md) | Contratos Zod → OpenAPI → cliente gerado | aceito |
| [006](006-auth-propria-refresh-rotativo.md) | Autenticação própria com refresh token rotativo | aceito |
| [007](007-app-unico-modo-lojista.md) | Um único app com modo lojista | aceito |
| [008](008-qr-totp-validacao-online.md) | QR com TOTP por resgate e validação online | aceito |
| [009](009-painel-spa-cloudflare-pages.md) | Painel web como SPA no Cloudflare Pages | aceito |
| [010](010-redis-bullmq.md) | Redis + BullMQ para filas, jobs e cache | aceito |
| [011](011-imagens-r2-upload-preassinado.md) | Imagens no Cloudflare R2 com upload pré-assinado | aceito |
| [012](012-convencoes-de-dados.md) | Convenções de dados | aceito |

Modelo para novos ADRs: Status · Data · Referência · Contexto · Decisão · Alternativas consideradas · Consequências.
