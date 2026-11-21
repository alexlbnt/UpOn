# ADR-005 — Contratos Zod → OpenAPI → cliente gerado

- **Status:** aceito
- **Data:** 2026-11-21
- **Referência:** `docs/PLANEJAMENTO_UPON.md` §2.2, §2.8 e §3.7

## Contexto

App, painel e API precisam concordar sobre formatos de requisição, resposta e erro. No projeto antigo, o contrato divergiu e havia mocks no lugar da API (M1, M3).

## Decisão

- Schemas **Zod**, enums e códigos de erro ficam **só em `packages/contracts`**.
- A API valida com esses schemas via **nestjs-zod**, que também gera o Swagger.
- A API exporta `openapi.json` (versionado em `docs/api/openapi.json`).
- O **Orval** gera `packages/api-client` (cliente + hooks TanStack Query) usado pelo app e pelo painel.
- Formulários usam os mesmos schemas com `react-hook-form` + `zodResolver`.
- O **CI falha** se o `openapi.json` gerado for diferente do versionado.

## Alternativas consideradas

- **class-validator + DTOs:** exige classes duplicadas das regras do front.
- **tRPC / ts-rest:** acoplam os clientes ao TypeScript e dificultam um contrato REST documentado e versionado (`/v1`).
- **Cliente escrito à mão:** volta ao risco de divergência.

## Consequências

- ✅ Uma fonte de verdade: uma regra de validação muda em um lugar só.
- ⚠️ Etapa de geração no fluxo de desenvolvimento e no CI.
- ⚠️ Os schemas precisam ser compatíveis com OpenAPI 3.1.
