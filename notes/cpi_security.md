# Cross Program Invocation Security

**Author:** Soubhagya | **Last updated:** 2026-01-20

## CPI Fundamentals

Programs invoke other programs via `invoke` or `invoke_signed`. The callee receives a subset of caller's account list with potentially elevated privileges if caller is a PDA signer.

## Attack Vectors

### Missing Program ID Validation
Callee program ID taken from user supplied account without verification. Attacker substitutes malicious program implementing compatible interface.

### Signer Privilege Leakage
Caller passes signer accounts to CPI. Malicious callee can perform operations as the user if caller does not restrict account mutability.

### Reentrancy
Callee CPIs back to caller before state update. Classic checks effects interactions pattern applies:

```rust
// Vulnerable
token::transfer(...)?;  // External call first
state.balance -= amount;  // State update after

// Secure
state.balance -= amount;
token::transfer(...)?;
```

### Arbitrary CPI
Program allows user to specify any target program for CPI. Attacker invokes token transfer or system program operations outside intended flow.

## Defense Patterns

1. Hardcode CPI target program IDs
2. Use `AccountInfo::is_signer` and `is_writable` checks
3. Update state before CPI (CEI)
4. Validate return data from CPI if used for control flow
5. Limit CPI depth (max 4 levels in practice)

## Forensic Indicators

- Unexpected program ID in inner instruction trace
- State change after token transfer CPI
- CPI to system program for account creation mid flow
