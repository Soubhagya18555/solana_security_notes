# SVM Internals: Security Relevant Architecture

**Author:** Soubhagya | **Last updated:** 2026-01-20

## Overview

The Solana Virtual Machine (SVM) executes Berkeley Packet Filter (eBPF) bytecode compiled from Rust via LLVM. Security researchers must understand the execution model to identify vulnerabilities in program logic, account validation, and compute budget abuse.

## Transaction Lifecycle

1. **Sigverify:** Ed25519 signature validation on all required signers
2. **Banking stage:** Parallel transaction scheduling by account write locks
3. **Program execution:** eBPF interpreter or JIT compiled execution
4. **Account commit:** State changes written if no error

Write lock conflicts serialize transactions touching the same accounts, creating predictable ordering exploitable for MEV.

## Account Model

Each account stores: lamports, data bytes, owner program, executable flag, rent epoch.

**Security invariant:** Only the owner program may modify account data. Violations occur when programs fail to verify account ownership before deserialization.

```rust
// Vulnerable: trusts client supplied token account
let token_account = Account::<TokenAccount>::try_from(&accounts[1])?;

// Secure: verify owner is SPL Token program
if accounts[1].owner != &spl_token::ID { return Err(ProgramError::InvalidAccountOwner); }
```

## Compute Budget

Default 200,000 CU per transaction, max 1.4M with request. Attackers craft transactions exhausting CU to grief protocols or manipulate execution order within block.

**Mitigation:** Profile CU usage, set instruction level limits, reject over budget routes.

## Syscalls

Programs interact with runtime via syscalls: `sol_invoke_signed`, `sol_memcpy`, `sol_log`. Misuse of `sol_invoke_signed` with attacker controlled seeds enables PDA forgery.

## Sealevel Parallelism

Solana's parallel execution requires explicit account declaration. Missing read only accounts in instruction metadata causes non deterministic execution across validators (consensus failure) or stale reads.

## Key Research Areas

- JIT compiler sandbox escapes (historical CVEs in BPF verifier)
- Account resizing attack surface
- LoadedAccountsDataSizeLimit bypass
- Feature gate activation timing attacks
