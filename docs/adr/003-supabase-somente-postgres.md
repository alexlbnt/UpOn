# ADR-003 — Supabase usado só como Postgres gerenciado

- **Status:** aceito
- **Data:** 2026-11-21
- **Referência:** `docs/PLANEJAMENTO_UPON.md` §3.4, §3.7 e RNF12

## Contexto

Precisamos de Postgres gerenciado com PostGIS, pooler e backups com PITR, sem amarrar o código a um fornecedor.

## Decisão

Usar o **Supabase apenas como banco PostgreSQL** (com PostGIS) e o pooler **Supavisor** em modo transação para a API, com conexão direta para migrations. **Não** usar Supabase Auth, Storage, Realtime, RLS nem Edge Functions. O código usa somente recursos de Postgres padrão.

## Alternativas consideradas

- **Neon:** padrão em outros projetos pessoais; também serviria, mas o Supabase foi escolhido aqui pelo pooler e PostGIS prontos.
- **RDS / Cloud SQL:** mais caros e com mais operação no início.
- **Usar o Supabase completo (Auth, Storage, RLS):** acelera o começo, mas amarra regras de autorização e sessão ao fornecedor.

## Consequências

- ✅ Migração simples para RDS ou Cloud SQL (dump/restore de Postgres padrão).
- ✅ Autorização e sessão sob controle da API (ADR-006).
- ⚠️ Perdemos recursos prontos do Supabase (aceito).
