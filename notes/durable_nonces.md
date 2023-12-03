# Durable Nonces Deep Dive

**Author:** Soubhagya | **Last updated:** 2026-01-28

## Purpose

Durable nonce transactions replace recent blockhash with a nonce account value allowing partially signed or offline signed transactions to remain valid until nonce advances.

## Account structure

Nonce account stores:

- `authority` pubkey authorized to advance nonce
- `nonce` hash value used as blockhash substitute
- Fee payer funds rent exempt balance

## Required instruction ordering

Valid durable nonce transaction **must** place `AdvanceNonceAccount` as the **first** instruction. Failure enables replay until nonce advances externally.

```
Instruction 0: AdvanceNonceAccount
Instruction 1..N: Business logic
```

## Threat scenarios

### Partial sign leak replay

Multisig holds partially signed tx bytes. Leak before co-signers approve. Attacker rebroadcasts if nonce not yet advanced.

**Mitigation:** Rotate nonce authority after session; track approved message hashes off chain.

### Nonce authority hijack

Attacker gains nonce authority via phishing Authorize equivalent on custodial wrapper.

**Mitigation:** Nonce authority equals multisig; monitor authority changes.

### Withdraw from nonce griefing

Attacker drains fee payer lamports from nonce account causing future txs to fail (DoS).

**Mitigation:** Maintain minimum balance alerts.

## Interaction with priority fees

Durable nonce txs still pay priority fees. Nonce advance does not consume blockhash expiry the same way but network congestion affects landing probability.

## Wallet implementation checklist

- [ ] Detect durable nonce txs by inspecting first instruction
- [ ] Display "offline / nonce" badge in preview
- [ ] Warn on signing nonce tx without co-signer quorum verified off chain
- [ ] After sign, mark message hash consumed in local audit log

## Forensic replay

```bash
# Identify nonce account from tx
solana transaction-history --nonce-account <PUBKEY>
# Check advance ordering in decoded instructions
```

## When to avoid durable nonces

Hot wallet online signing should use recent blockhash with short expiry. Reserve nonce flow for:

- Hardware wallet multisig ceremonies
- Custodian HSM offline queues
- Cross timezone co-signer workflows

## Related

- solana_attack_corpus: `durable_nonce_replay`
- SCV_027 durable nonce authority bypass
