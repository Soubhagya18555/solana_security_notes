# Program Derived Address Attacks

**Author:** Soubhagya | **Last updated:** 2026-01-20

## PDA Mechanics

PDAs are addresses derived from seeds + program ID, lying off the Ed25519 curve (no private key). Programs sign for PDAs via `invoke_signed` with canonical bump seed.

## Attack Vectors

### Seed Collision
Attacker finds alternative seed combination producing same PDA under different program. Rare but possible with weak seed design.

### Bump Seed Brute Force
Program stores bump in account data. If not validated, attacker supplies wrong bump causing PDA derivation failure or wrong PDA.

```rust
let (pda, bump) = Pubkey::find_program_address(&[b"vault", user.key().as_ref()], program_id);
// Always use canonical bump from find_program_address
```

### Missing PDA Ownership Check
Account passed as PDA but not verified against expected seeds. Attacker passes their own PDA from different seed path.

### PDA as Signer Confusion
Program treats user wallet as PDA signer. Must verify `is_signer` on correct account.

## Canonical Patterns

1. Derive PDA with `find_program_address`
2. Store bump in account, validate on each access
3. Use seed prefixes preventing cross program collision
4. Never accept bump from untrusted input without validation
