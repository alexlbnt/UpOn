# ADR-006 — Autenticação própria com refresh token rotativo

- **Status:** aceito
- **Data:** 2026-11-21
- **Referência:** `docs/PLANEJAMENTO_UPON.md` §2.6, §3.7, RNF01–RNF03

## Contexto

O projeto precisa de login real, sessão persistente e segura no app e no painel, e revogação imediata. O app antigo guardava `"true"` no AsyncStorage como sessão (A1). Supabase Auth não é usado (ADR-003).

## Decisão

Autenticação implementada **no NestJS**:

- **Access token** JWT **ES256**, 15 min, com `sub`, `sid`, `platformRole` e `kid`. Não carrega papéis de lojista (checados no banco a cada ação).
- **Refresh token** opaco de 256 bits, 30 dias, com **rotação a cada uso**; o banco guarda só o hash SHA-256.
- **Detecção de reuso:** refresh já rotacionado apresentado de novo revoga **toda a família** (`familyId`).
- **Senhas** com **Argon2id**; bloqueio progressivo após falhas (Redis).
- **App:** tokens no **SecureStore**. **Painel:** refresh em cookie `__Host-` httpOnly, Secure, SameSite=Strict; access token só em memória; CSRF por checagem de `Origin` + cabeçalho próprio.
- **Autorização** sempre na API: `JwtAuthGuard` global (`@Public()` nas rotas abertas), `PlatformRoleGuard` e políticas de domínio consultando `MerchantMember`.

## Alternativas consideradas

- **Supabase Auth:** amarra o código ao fornecedor (contra o ADR-003).
- **Passport:** adiciona camadas sem ganho aqui.
- **Better Auth:** bom, mas tem modelo próprio de sessão.

## Consequências

- ✅ Controle total e revogação imediata.
- ⚠️ A responsabilidade de segurança é nossa: testes de integração obrigatórios para rotação, reuso e expiração; tokens nunca em log.
