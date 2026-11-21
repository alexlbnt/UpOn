# ADR-009 — Painel web como SPA no Cloudflare Pages

- **Status:** aceito
- **Data:** 2026-11-21
- **Referência:** `docs/PLANEJAMENTO_UPON.md` §3.2 e §3.7

## Contexto

O painel (estabelecimentos e administração da plataforma) fica atrás de login, não precisa de SEO, e a regra de negócio deve ficar só no backend.

## Decisão

**React + Vite (SPA) + TypeScript**, com **TanStack Router** (rotas tipadas), **TanStack Query** (hooks gerados pelo Orval), **shadcn/ui + Tailwind** e TanStack Table, hospedado no **Cloudflare Pages** (previews por PR). Sem SSR e sem regra de negócio no front: o painel só esconde botões; a API autoriza.

## Alternativas consideradas

- **Next.js:** SSR e SEO não são necessários atrás de login, e server actions tentam colocar regra no front.
- **React Router:** bom, mas com tipagem menos forte que o TanStack Router.
- **Vercel / Netlify:** equivalentes; o Pages concentra borda, DNS e R2 na Cloudflare.

## Consequências

- ✅ Simples, barato e rápido de servir pela CDN.
- ⚠️ Páginas públicas com SEO, se forem necessárias (D14), serão outro app.
