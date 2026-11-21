# ADR-001 — Monorepo com pnpm workspaces e Turborepo

- **Status:** aceito
- **Data:** 2026-11-21
- **Referência:** `docs/PLANEJAMENTO_UPON.md` §2.2 e §3.7

## Contexto

O app mobile, o painel web e a API compartilham tipos, schemas de validação e algumas regras puras. No projeto antigo (`App_Upon`), o app e a API ficaram fora de sincronia: contratos divergentes e mocks no lugar das chamadas reais (problemas M1 e M3).

## Decisão

Um único repositório com `apps/*` (`mobile`, `admin`, `api`) e `packages/*` (`contracts`, `api-client`, `domain-rules`, `design-tokens`, `config`), gerenciado com **pnpm workspaces** e orquestrado pelo **Turborepo** (tarefas com cache: lint, typecheck, test, build).

## Alternativas consideradas

- **Repositórios separados:** exigem publicar e versionar os pacotes de contrato à parte; é fácil o app e a API saírem de sincronia.
- **Nx:** mais recursos, mas mais configuração e conceitos do que o projeto precisa.

## Consequências

- ✅ Contrato único e mudanças atômicas (API + clientes no mesmo PR).
- ✅ Cache do Turborepo acelera o CI.
- ⚠️ O CI precisa de cache configurado.
- ⚠️ Expo + pnpm em monorepo exige atenção à resolução de dependências nativas (risco R9). Validar na Fase 0 e manter versões alinhadas com `npx expo install --check`.
