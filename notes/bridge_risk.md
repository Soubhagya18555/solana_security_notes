# Bridge Risk on Solana

**Author:** Soubhagya | **Last updated:** 2026-01-28

## Bridge architecture classes

| Type | Trust model | Example patterns |
|------|-------------|------------------|
| Guardian multisig | N of M off chain signers | Wormhole, Allbridge |
| Light client | On chain header verification | ZK bridge research |
| Liquidity network | LP pools both sides | Hashflow style |
| Lock and mint | Custodial lock on source | Centralized bridges |

Solana destination programs most commonly verify guardian signatures or multisig posted messages.

## Universal failure modes

### Message replay (SCV_030)

Valid proof processed twice minting double wrapped assets.

**Defense:** `ProcessedMessages` PDA keyed by `(emitter_chain, sequence)`.

### Guardian compromise

Threshold keys stolen or colluding guardians sign fraudulent mint.

**Defense:** Monitor guardian set changes; rate limit mint per message size; circuit breaker on TVL outflow.

### Wrapped asset upgrade

Upgrade authority on wrapped token program mints infinite supply.

**Defense:** SCV_040 buffer authority controls; immutable mint after deployment option.

### Relayer censorship

Permissioned relayer fails to post valid messages while competing txs process.

**Defense:** Permissionless post instruction with fee incentive.

## Solana specific considerations

- Mint authority often held by bridge PDA using `invoke_signed`
- CPI into Token program must validate mint account matches expected wrapped mint for asset
- Compute budget: verify signatures on chain is CU expensive; watch DoS via large guardian sets

## Economic risk matrix

| TVL | Recommended controls |
|-----|------------------------|
| < $5M | Multisig + pause admin |
| $5M - $50M | Timelock upgrade + rate limits |
| > $50M | Independent monitoring + insurance + formal verification of mint invariant |

## Forensic fund tracing

1. Identify mint instruction in bridge program logs
2. Trace wrapped mint to DEX swap exit
3. Cluster destination wallets via shared funding source
4. Check source chain lock event correlation (timestamp, amount)

## Incident playbooks

| Symptom | Immediate action |
|---------|------------------|
| Duplicate sequence processed | Pause mint instruction via admin |
| Guardian key leak rumor | Rotate guardian set via governance |
| Unexpected upgrade | Fork diff bytecode; halt frontend |

## Related samples

- web3_threat_model_framework: `examples/bridge_threat_model.md`
- SCV_030 bridge message replay
