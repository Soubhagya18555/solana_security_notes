# Governance Attack Vectors

**Author:** Soubhagya | **Last updated:** 2026-01-20

## Attack Types

### Flash Loan Governance
Borrow governance tokens, vote on proposal, repay loan in same transaction. Mitigated by vote snapshot at proposal creation block.

### Proposal Spam
Flood governance with proposals to hide malicious action or exhaust reviewer attention.

### Timelock Bypass
Exploit emergency function or proxy upgrade outside governance timelock.

### Vote Buying
Bribe token holders for delegate votes. Off chain but affects on chain outcomes.

### Low Quorum Exploitation
Malicious proposal passes with minimal participation during low activity period.

## Defense Architecture

```
Proposal creation (deposit required)
    → Voting period (snapshot at creation)
    → Queue (timelock delay)
    → Execution (multisig or program call)
```

## Monitoring

- Alert on proposals modifying: upgrade authority, fee parameters, treasury withdrawals
- Track governance token concentration changes before votes
- Monitor timelock queue for unexpected executions
