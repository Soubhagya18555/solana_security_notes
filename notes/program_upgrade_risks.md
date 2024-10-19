# Program Upgrade Risks

**Author:** Soubhagya | **Last updated:** 2026-01-20

## Upgrade Models

| Model | Authority | Risk |
|-------|-----------|------|
| Immutable | None | No patch path for bugs |
| Single key | One pubkey | Key compromise = full control |
| Multisig | M of N signers | Lower single point failure |
| Timelock + multisig | Delayed execution | Community review window |
| Governance | Token vote | Flash loan vote attacks |

## Upgrade Attack Scenarios

### Malicious Upgrade
Attacker compromises upgrade authority, deploys drain logic. Users interacting with program address (unchanged) now hit malicious code.

### Emergency Abuse
Legitimate emergency upgrade mechanism used to rug without governance.

### Proxy Pattern Confusion
Users verify program A but interactions routed to implementation B via proxy.

## User Protection

- Display program hash in wallet before interaction
- Monitor upgrade authority transactions
- Verified build registry comparison
- Timelock announcements for scheduled upgrades

## Developer Best Practices

- Multisig upgrade authority (minimum 3 of 5)
- 48+ hour timelock for non emergency upgrades
- Immutable core logic with upgradeable config only
- Publish upgrade diff before execution
- Bug bounty covering upgrade authority compromise
