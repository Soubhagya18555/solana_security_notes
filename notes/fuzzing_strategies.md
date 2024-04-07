# Fuzzing Strategies for Solana Programs

**Author:** Soubhagya | **Last updated:** 2026-01-28

## Objectives

Fuzzing finds account validation bugs, panic paths, and compute blowups that manual audit misses. Target instruction handlers, not BPF loader itself.

## Tooling landscape

| Tool | Approach | Best for |
|------|----------|----------|
| Trident | Anchor native fuzz | Instruction account metas |
| Honggfuzz / libFuzzer | Rust entrypoint fuzz | Pure Rust parsers |
| Mollusk | Lightweight SVM invoke | Fast regression |
| cargo fuzz | LLVM fuzzer | Crypto / deserialize |

## Trident setup pattern

```rust
#[fuzz_test]
fn fuzz_deposit(ctx: FuzzContext) {
    trident::fuzz!(|accounts: DepositAccounts, data: DepositData| {
        let result = deposit(ctx, accounts, data);
        // Invariants
        assert!(vault_balance_conserved());
    });
}
```

## Account meta mutation strategy

For each instruction randomly mutate:

- Signer bit on authority accounts
- Writable bit on vault accounts
- Owner program id substitution
- Account data length (oversized, zero, truncated)
- Duplicate account positions

Maps directly to SCV_001, SCV_002, SCV_017 coverage.

## Corpus seeding

Seed fuzz inputs from:

- Mainnet transaction samples (sanitized)
- Audit finding PoC bytes
- IDL generated arbitrary structs

 improves time to first crash.

## Invariant oracles

Define properties that must hold after every invocation:

```rust
fn invariant_total_supply() {
    assert_eq!(mint.supply, sum(balances));
}
fn invariant_vault_lamports() {
    assert!(vault.lamports >= recorded_total);
}
```

## Compute budget fuzzing

Randomize:

- Number of remaining accounts (SCV_038)
- CPI depth chains
- Loop iteration counts

Watch for `ComputationalBudgetExceeded` versus silent partial execution.

## Differential fuzzing

Run same input on:

- Program version N and N+1 after upgrade
- Compare state diff; unexpected diff flags regression

## CI integration

```yaml
fuzz:
  script:
    - cargo fuzz run deposit -- -max_total_time=300
  allow_failure: false
  artifacts:
    paths:
      - fuzz/artifacts/crash/*
```

Store crash reproducers as regression tests.

## Limitations

Fuzzing rarely finds:

- Economic oracle manipulation (needs structured scenario)
- Cross transaction race conditions (needs multi tx harness)
- Off chain wallet UI bugs

Combine with formal methods for critical math (interest rate models).

## Coverage goals

| SCV category | Fuzz focus |
|--------------|------------|
| Access control | Signer/writer bit flip |
| CPI | Arbitrary program id in remaining |
| Type confusion | Account data random bytes |
| Reentrancy | Multi invoke same ix in one tx harness |

## Related

- SCV_019 compute exhaustion
- solana_common_vulnerabilities DETECTION_GUIDE fuzz row
- CHECKLIST_ONCHAIN.md testing section
