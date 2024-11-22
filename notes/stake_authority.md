# Stake Authority Security

**Author:** Soubhagya | **Last updated:** 2026-01-28

## Stake account meta

Stake account metadata contains two distinct authorities:

| Field | Capability |
|-------|------------|
| Staker authority | Delegate stake to vote account, authorize new staker |
| Withdrawer authority | Withdraw lamports after deactivation epoch |

Confusing these in UI or program logic enables full stake theft.

## Delegation versus withdrawal

Delegation (`DelegateStake`) assigns vote account and activates stake. Does not move funds immediately.

Withdrawal (`Withdraw`) requires deactivation period (epoch boundary) unless stake already inactive.

Attackers target **withdrawer** rotation via phishing because it is the eventual fund exit key.

## Authorize instruction risk

`Authorize` can change staker or withdrawer to arbitrary pubkey. Staking dApps must:

1. Never label Authorize as "boost rewards" without showing new pubkey
2. Block Authorize to pubkeys created same day
3. Require hardware wallet for withdrawer changes

## Custodial staking patterns

Exchanges pooling stake behind single authority create systemic risk. On chain forensic: single withdrawer controlling thousands of stake accounts.

## Vote account selection

Malicious vote accounts with high commission or no uptime steal rewards without direct withdrawer compromise. Validate vote account history:

- Commission rate
- Credits earned last epoch
- Identity pubkey age

## Forensic indicators

```
Authorize(Withdrawer) -> DelegateStake -> Withdraw
within minimal deactivation window abuse (if lockup misconfigured)
```

## Program integration

Protocols accepting stake as collateral must read:

- `stake.delegation.stake` amount
- Activation epoch and deactivation epoch
- Lockup unix timestamp if present

Never trust off chain staking balance without on chain meta read.

## Mitigation checklist

- [ ] Separate UI color coding for staker vs withdrawer actions
- [ ] Timelock on withdrawer change notifications email/push
- [ ] Monitor Authorize events for integrated custodial wallets
- [ ] SCV_029 test cases in staking program audits

## Related

- solana_attack_corpus: `stake_authority_hijack`
- SCV_029 stake account authority confusion
