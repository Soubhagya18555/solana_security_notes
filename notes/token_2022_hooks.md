# Token 2022 Extensions and Hook Security

**Author:** Soubhagya | **Last updated:** 2026-01-20

## Token 2022 Overview

SPL Token 2022 extends the original token program with transfer hooks, confidential transfers, transfer fees, and metadata pointers. Each extension introduces new attack surface.

## Transfer Hooks

Tokens can specify a hook program invoked on every transfer. Hook receives sender, receiver, mint, and amount.

### Risks
- **Denial of service:** Hook program fails, blocking all transfers
- **State manipulation:** Hook modifies unrelated accounts passed via remaining accounts
- **Reentrancy:** Hook CPIs back to token program

### Mitigation
- Audit hook program independently
- Wallets should simulate hook effects before signing
- Protocols should block deposits of tokens with untrusted hooks

## Transfer Fee Extension

Fee deducted on transfer to fee collector account. Risk: fee config changed by mint authority after user approval.

## Confidential Transfer

Zero knowledge proofs hide transfer amounts. Risks: proof verification bugs, key compromise revealing history.

## Metadata Pointer

Mint points to external metadata account. Risk: metadata changed post mint to impersonate legitimate token.

## Wallet Implications

Display Token 2022 extensions prominently. Warn on transfer hook tokens. Verify mint metadata against registry.
