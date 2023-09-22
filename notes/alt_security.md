# Address Lookup Table Security

**Author:** Soubhagya | **Last updated:** 2026-01-28

## Background

Versioned (v0) transactions compress account keys via Address Lookup Tables (ALTs). Each lookup replaces 32 byte pubkey with 1 byte index into an on chain table account.

## Threat model

ALTs are mutable by their authority until deactivated. Any party trusting resolved addresses at time T without revalidation at T+delta accepts substitution risk.

## Attack sequence

```
1. Attacker creates ALT with [DEX_pool, user_ATA, token_program]
2. Victim previews swap resolving indices to benign accounts
3. Attacker submits ExtendLookupTable replacing index 1 with drainer ATA
4. Victim already signed message referencing indices not pubkeys
5. Execution drains user_ATA via substituted account meta
```

Slot window can be a single block on Solana.

## Authority considerations

| Authority state | Risk |
|-----------------|------|
| Attacker owned | Full table mutation |
| Protocol multisig | Lower risk if timelocked |
| Deactivated table | Reference fails safely |

Wallets must display ALT authority pubkey and warn on unknown authorities.

## Resolution requirements

### At preview time

Resolve all indices to pubkeys. Display static keys and ALT resolved keys in separate sections.

### At sign time

Re-fetch ALT account data. Compare hash to preview hash. Abort on mismatch.

### At broadcast time

Optional third fetch if sign to send delay exceeds 400ms.

## Deactivation and closure

Deactivated ALTs remain readable for existing references but cannot extend. Close reclaims rent. Wallets should handle deactivated table errors gracefully.

## Program developer guidance

On chain programs cannot resolve ALTs (runtime expands before entry). Client and indexer layers bear validation burden. Document expected ALT pubkeys in integration specs.

## Detection

- ExtendLookupTable instruction within 2 slots of high value v0 tx
- Resolved writable set differs between simulation providers
- User telemetry simulation_execution account mismatch

## Mitigation summary

1. Never cache ALT resolution across signing steps
2. Pin table content hash in signing audit log
3. Block signing when ALT authority not on allowlist for integrated protocols
4. Indexers flag table mutation + victim tx correlation

## Related entries

- solana_attack_corpus: `alt_lookup_swap`
- SCV_026 missing ALT validation
- WTM_029 versioned transaction lookup table swap
