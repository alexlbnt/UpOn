# ADR-008 — QR com TOTP por resgate e validação online

- **Status:** aceito
- **Data:** 2026-11-21
- **Referência:** `docs/PLANEJAMENTO_UPON.md` §2.7, §3.7, RN14–RN23, RNF18

## Contexto

A validação no caixa precisa de uso único, expiração curta e proteção contra print, e o consumidor pode estar sem sinal dentro da loja. O QR do app antigo era forjável e validado localmente (A6). Decisão de produto: validação **sempre online** do lado do lojista no MVP.

## Decisão

- Cada resgate tem um **segredo próprio** (`claimSecret`, 160 bits), entregue uma única vez ao dono e guardado no SecureStore; no servidor, cifrado em repouso (AES-GCM, chave fora do banco).
- O app gera um **código rotativo TOTP** (RFC 6238, HMAC-SHA1, passo de 30 s, 8 dígitos) **no aparelho**, inclusive offline.
- Payload do QR: `UPON1.<claimShortCode>.<totp8>`; código digitável de reserva: `shortCode` + 6 dígitos.
- Lojista valida **online** em duas etapas: `preview` (confere status, validade, unidade, vínculo, RN20 e TOTP ±1 passo; trava o resgate por 60 s no Redis) → `confirm` (transação `CLAIMED → REDEEMED` + `UNIQUE(redemption.claimId)`, com `Idempotency-Key`).
- Passo TOTP usado é marcado no Redis (TTL 90 s): o mesmo código nunca vale duas vezes.
- A lógica TOTP fica em `packages/domain-rules`, o mesmo código no app e na API.

## Alternativas consideradas

- **Token assinado emitido pelo servidor na hora:** exige internet do consumidor no caixa.
- **QR estático por cupom:** um print vale até o cupom expirar.
- **Validação offline no lojista:** adiada para a F5, a reavaliar com dados do piloto.

## Consequências

- ✅ O consumidor offline consegue mostrar o QR; um print expira em ~60 s; uso único garantido pelo banco.
- ⚠️ O relógio do aparelho precisa estar razoavelmente certo: janela ±1 passo + correção de desvio pelo horário do servidor (risco R6).
- ⚠️ O lojista precisa de internet (risco R7).
