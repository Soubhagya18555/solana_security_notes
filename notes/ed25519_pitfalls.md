# Ed25519 Pitfalls on Solana

**Author:** Soubhagya | **Last updated:** 2026-01-28

## Overview

Solana uses ed25519 for transaction signatures and supports on chain verification via the Ed25519Program. Misimplementation at either layer creates authorization bypass, replay, and malleability risk.

## Runtime transaction signatures

The Agave runtime validates transaction signatures before program execution. Malleated signatures (non canonical S values) are rejected at ledger level for standard transactions. Do not assume this protection extends to:

- Ed25519Program syscall based custom auth
- Off chain message verification in backends
- Cross chain bridge guardian signature checks

## Canonical signature requirements (ZIP-215)

Valid signatures must use canonical S where S < L/2 for curve order L. Libraries like ed25519_dalek reject malleated forms by default in recent versions. Verify your dependency version.

```rust
// Off chain verifier must reject malleated pairs
use ed25519_dalek::{Signature, Verifier, VerifyingKey};

pub fn verify_canonical(msg: &[u8], sig_bytes: &[u8], vk: &VerifyingKey) -> bool {
    let sig = match Signature::from_slice(sig_bytes) {
        Ok(s) => s,
        Err(_) => return false,
    };
    vk.verify(msg, &sig).is_ok()
}
```

## Message binding failures

| Pitfall | Consequence |
|---------|-------------|
| Verify signature without domain separator | Cross protocol replay |
| Track used signatures by raw bytes not message hash | Malleability double spend |
| Omit Instructions sysvar index binding | Hide preceding tx instruction |
| Accept signatures over partial message | Instruction substitution |

Always include domain prefix for off chain messages (SIWS pattern):

```
\x19Solana Signed Message:\n{length}{message}
```

## Ed25519Program on chain

Programs invoking Ed25519Program must:

1. Pass correct instruction data layout (signature offset, pubkey offset, message offset)
2. Verify the verified instruction index matches expected outer or inner instruction
3. Not treat verification success as substitute for signer check on accounts

## HD derivation edge cases

SLIP-0010 ed25519 derivation produces hardened paths only. Common pitfalls:

- Mixing secp256k1 BIP44 paths with ed25519 keys
- Non hardened derivation (unsupported) implemented incorrectly in custom wallets
- Seed phrase typo with checksum not validated before derivation

## Batch verification

Validators batch verify signatures for performance. Custom tooling using single verify may accept edge case encodings batch path rejects. Test vectors from ZIP-215 and RFC 8032.

## Forensic indicators

- Two signatures over identical message with different S bytes
- Authorization account consumed twice in same slot different sig bytes
- Off chain API accepts signature rejected by on chain Ed25519Program

## Mitigation checklist

- [ ] Use runtime signers not Ed25519Program when possible
- [ ] Canonical S enforcement in all custom verifiers
- [ ] Message hash keyed consumption ledger in program state
- [ ] Domain separated off chain message formats
- [ ] Dependency audit on ed25519_dalek / libsodium versions

## References

- ZIP-215 canonical signatures
- Solana Ed25519Program documentation
- RFC 8032 Edwards curve signatures
