# ADR-011 — Imagens no Cloudflare R2 com upload pré-assinado

- **Status:** aceito
- **Data:** 2026-11-21
- **Referência:** `docs/PLANEJAMENTO_UPON.md` §2.1, §3.4 e §3.7

## Contexto

Ofertas e estabelecimentos têm imagens. Passar arquivos pela API aumenta latência, memória e custo de banda.

## Decisão

A API emite **URL pré-assinada** (`POST /v1/media/upload-url`); o cliente envia o arquivo **direto ao R2**; um job valida o arquivo e gera as variações (Cloudflare Images/Transformations ou `sharp`). As imagens são servidas pela CDN da Cloudflare. Em desenvolvimento, o **MinIO** simula o R2 (API compatível com S3).

## Alternativas consideradas

- **Upload pela API:** simples, mas pesa na API.
- **S3 + CloudFront:** equivalente tecnicamente, mas o R2 não cobra saída de dados.
- **Supabase Storage:** contra o ADR-003.

## Consequências

- ✅ API leve e CDN barata; código portável para qualquer storage S3-compatível.
- ⚠️ É preciso um job de limpeza de uploads órfãos.
