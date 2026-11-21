# ADR-010 — Redis + BullMQ para filas, jobs e cache

- **Status:** aceito
- **Data:** 2026-11-21
- **Referência:** `docs/PLANEJAMENTO_UPON.md` §3.3, §3.4 e §3.7

## Contexto

O sistema precisa de jobs (ativar e expirar ofertas, expirar resgates e liberar estoque), envio de e-mail e push, idempotência, rate limit, cache de listagens e controle dos passos TOTP usados.

## Decisão

Um **Redis** gerenciado com **BullMQ** (`@nestjs/bullmq`) para filas e jobs repetidos (cron), e o mesmo Redis para cache, idempotência (24 h), rate limit (`@nestjs/throttler`) e passos TOTP, separados por bancos lógicos ou prefixos. Os consumidores rodam no processo `worker` (ADR-002).

## Alternativas consideradas

- **pg-boss:** usa só o Postgres (menos infraestrutura), mas tem menos recursos e aumenta a carga no banco.
- **Filas gerenciadas (SQS etc.):** amarram a um provedor e não resolvem cache e rate limit.

## Consequências

- ✅ Um componente resolve vários problemas; painel Bull Board para inspecionar filas.
- ⚠️ Redis vira dependência crítica: monitorar. A API degrada sem cache, mas não perde dados (a fonte de verdade é o Postgres).
