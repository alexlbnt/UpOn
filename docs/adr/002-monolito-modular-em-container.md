# ADR-002 — Monólito modular em container

- **Status:** aceito
- **Data:** 2026-11-21
- **Referência:** `docs/PLANEJAMENTO_UPON.md` §2.1, §2.3 e §3.7

## Contexto

Equipe pequena (um desenvolvedor), com necessidade de workers, filas, jobs agendados e conexões longas. Serverless dificulta filas e conexões persistentes; microsserviços trazem custo operacional sem benefício nesta fase.

## Decisão

Backend **NestJS** como **monólito modular**, empacotado em **uma imagem Docker** com **dois processos**: `api` (HTTP) e `worker` (consumidores BullMQ e cron), com entradas separadas (`main.ts` e `worker.ts`). Hospedagem em container contínuo (Railway, com Render e Fly como alternativas).

Cada módulo segue as camadas `http/` → `application/` → `domain/` ← `infra/`. A **regra de negócio fica só em `domain/`**, sem dependência de Nest nem de Prisma.

## Alternativas consideradas

- **Funções serverless:** ruins para workers, filas e conexões longas com o Postgres.
- **Microsserviços:** operação e observabilidade caras para uma equipe de uma pessoa.
- **Fastify puro / Hono:** mais leves, mas teríamos de montar a estrutura (DI, guards, filas) que o Nest já traz.

## Consequências

- ✅ Simples de operar, portável (qualquer host de containers) e com escala separada para `api` e `worker`.
- ✅ Módulos com fronteiras claras podem virar serviços no futuro.
- ⚠️ As fronteiras entre módulos exigem disciplina (lint de imports entre módulos).
