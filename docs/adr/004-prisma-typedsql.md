# ADR-004 — Prisma com TypedSQL para PostGIS

- **Status:** aceito
- **Data:** 2026-11-21
- **Referência:** `docs/PLANEJAMENTO_UPON.md` §2.5, §3.3, §3.7 e risco R8

## Contexto

Precisamos de migrations confiáveis, tipagem forte e consultas geográficas (PostGIS). O projeto antigo usava TypeORM com `synchronize: true` (A8) e tipagem fraca.

## Decisão

**Prisma** para o modelo e as migrations. A coluna `location` é declarada como `Unsupported("geography(Point,4326)")`, com índice GIST criado na migration, e as consultas geográficas usam **TypedSQL** (SQL tipado) isolado no repositório de geolocalização.

Migrations são sempre geradas pelo Prisma e revisadas; migration aplicada nunca é editada; mudanças destrutivas em dois passos (expandir → migrar → contrair).

## Alternativas consideradas

- **Drizzle:** lida melhor com PostGIS e SQL bruto, mas é menos familiar.
- **TypeORM:** causou os problemas do app antigo (`synchronize`, tipagem fraca).

## Consequências

- ✅ Produtividade, tipagem e migrations previsíveis.
- ⚠️ Geolocalização fica fora do client do Prisma.
- ⚠️ Reavaliar Drizzle se o SQL bruto crescer muito.
