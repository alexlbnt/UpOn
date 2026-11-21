# ADR-007 — Um único app com modo lojista

- **Status:** aceito
- **Data:** 2026-11-21
- **Referência:** `docs/PLANEJAMENTO_UPON.md` §2.4 e §3.7

## Contexto

O funcionário do estabelecimento valida cupons no caixa. Uma mesma pessoa pode ser consumidora e membro de um ou mais estabelecimentos; o papel de lojista é um **vínculo** (`MerchantMember`), não um tipo de conta.

## Decisão

O **mesmo app Expo** atende consumidor e lojista. O Expo Router separa os grupos `(consumer)` e `(merchant)` com `Stack.Protected`: `(merchant)` exige sessão **e** algum vínculo ativo de membro. A permissão real continua sendo checada na API (RNF06).

## Alternativas consideradas

- **Dois apps (consumidor e lojista):** duas publicações nas lojas, duas bases de código e duas revisões.
- **Validação só pelo painel web:** pior no caixa (câmera, mobilidade).

## Consequências

- ✅ Uma publicação nas lojas e um só código.
- ⚠️ App um pouco maior.
- ⚠️ Papéis e trocas de modo precisam ser bem testados (E2E com Maestro).
