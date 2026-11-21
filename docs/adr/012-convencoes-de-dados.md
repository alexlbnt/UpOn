# ADR-012 — Convenções de dados

- **Status:** aceito
- **Data:** 2026-11-21
- **Referência:** `docs/PLANEJAMENTO_UPON.md` §2.5, §3.7 e RNF22

## Contexto

O app antigo guardava datas, distâncias e dinheiro como texto e misturava tipos de id (M7), o que quebrava ordenação, relatórios e cálculos.

## Decisão

- **Ids:** UUID v7 (ordenáveis por tempo).
- **Datas:** `timestamptz` em **UTC** no banco e na API (ISO 8601); exibição em `America/Sao_Paulo`.
- **Dinheiro:** **centavos inteiros** (`int`, campos com sufixo `Cents`); formatação só na exibição.
- **Enums** no banco, espelhados em `packages/contracts`.
- **Distância** nunca é gravada; é calculada na consulta (PostGIS).
- **Integridade no banco:** `UNIQUE` e `CHECK` para as invariantes críticas (por exemplo, `UNIQUE(redemption.claimId)`, `CHECK(claimedCount <= totalStock)`).

## Alternativas consideradas

- **UUID v4 / ids sequenciais:** v4 não é ordenável; sequenciais expõem volume e facilitam enumeração.
- **`decimal` para dinheiro:** correto, mas mais sujeito a erros de conversão em JS do que inteiros.

## Consequências

- ✅ Consultas, ordenação e relatórios corretos.
- ⚠️ Conversões na exibição (moeda e fuso) precisam de utilitários compartilhados.
