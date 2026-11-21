# UpOn

Plataforma de cupons de desconto locais. Estabelecimentos publicam ofertas; consumidores resgatam cupons para a carteira no app e os usam no caixa com um QR rotativo, validado pelo funcionário no modo lojista do mesmo app.

> Em desenvolvimento — Fase 0 (fundação).

## Stack

- **App:** Expo (React Native) + Expo Router, NativeWind, TanStack Query
- **Painel:** React + Vite, TanStack Router/Query, shadcn/ui
- **API:** NestJS + Fastify, Prisma, Zod (contratos compartilhados), BullMQ
- **Dados e infraestrutura:** PostgreSQL + PostGIS, Redis, Cloudflare (Pages, R2), Docker
- **Monorepo:** pnpm workspaces + Turborepo

## Como rodar

Requisitos: Node (versão em `.nvmrc`), pnpm via Corepack e Docker.

```bash
corepack enable
pnpm install
cp .env.example .env
docker compose -f infra/docker/docker-compose.yml up -d
pnpm dev
```

## Documentação

- [Planejamento](docs/PLANEJAMENTO_UPON.md) — requisitos, arquitetura e plano de implementação
- [ADRs](docs/adr/) — decisões de arquitetura
- [CLAUDE.md](CLAUDE.md) — regras do repositório e status atual
