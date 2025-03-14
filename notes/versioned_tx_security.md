# Versioned Transaction Security

**Author:** Soubhagya | **Last updated:** 2026-01-20

## v0 Transactions

Versioned transactions (v0) support Address Lookup Tables (ALTs) compressing account keys from 32 bytes to 1 byte index. Essential for complex DeFi interactions but introduces new attack surface.

## Lookup Table Attacks

### Table Content Swap
Attacker creates ALT with benign addresses. User previews transaction resolving to legitimate accounts. Attacker modifies ALT on chain before execution to point indices at drain accounts.

**Defense:** Resolve and display all ALT entries at signing moment. Reject if table modified since preview.

### Stale Table Reference
ALT deactivated but still referenced. Transaction fails or behaves unexpectedly.

## Message Format

v0 message includes: header, static account keys, recent blockhash, compiled instructions, address table lookups.

Wallets must decode compiled format for human readable preview. Failure to parse leads to blind signing.

## Implementation Checklist

- [ ] Support v0 deserialization in simulator
- [ ] Resolve all ALT accounts before preview
- [ ] Display static vs lookup resolved accounts separately
- [ ] Verify ALT authority before trusting contents
- [ ] Handle max account limit (64 total)
