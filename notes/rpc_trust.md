# RPC Trust and Simulation Integrity

**Author:** Soubhagya | **Last updated:** 2026-01-28

## Threat model

RPC providers are semi trusted. They observe all queries (privacy leak) and can return arbitrary JSON (integrity attack). Wallets and protocols must treat RPC responses as untrusted hints verified where possible.

## Attack vectors

### Balance spoofing (WTM_005)

Attacker controlled RPC reports inflated token balance inducing user to send real assets off platform expecting larger holdings.

### Simulation manipulation (WTM_020)

`simulateTransaction` returns fabricated logs showing "You receive 100 USDC" while execution transfers user USDC out.

### Blockhash withholding

RPC refuses recent blockhash preventing tx landing while attacker front runs with insider mempool view (where available).

### History pruning

RPC returns null for `getTransaction` hindering victim forensic response (availability not integrity).

## Multi RPC consensus pattern

```typescript
async function simulateConsensus(tx: Transaction, endpoints: string[]) {
  const results = await Promise.all(
    endpoints.map((url) => connection(url).simulateTransaction(tx))
  );
  const hashes = results.map((r) => hashLogs(r.value.logs));
  if (new Set(hashes).size !== 1) {
    throw new Error("simulation_divergence");
  }
  return results[0];
}
```

Require agreement on:

- `err` field null status
- Token balance delta vector
- Inner instruction program ids

## Local verification

 Deserialize transaction message locally. Never trust RPC for:

- Account keys in message (read from signed bytes)
- Instruction program ids
- Signer bitmap

## TLS and pinning

| Risk | Mitigation |
|------|------------|
| Corporate SSL inspect | Pin SPKI for default providers |
| DNS hijack | DoH + certificate transparency monitoring |
| Self signed MITM | Reject non system CA on release builds |

## Private RPC tradeoffs

Dedicated node reduces public exposure but concentrates trust in operator. Document residual trust assumptions in threat models.

## Protocol guidance

Backend keepers using single RPC should failover with state comparison:

```bash
solana balance <VAULT> --url $RPC_A
solana balance <VAULT> --url $RPC_B
# abort if mismatch
```

## Detection signals

- User report balance mismatch versus explorer
- Simulation err null on provider A err on provider B
- Blockhash age > 90 seconds on active network

## Related

- SCV_031 RPC simulation trust
- WTM_005, WTM_020, WTM_053
- solana_attack_corpus `alt_lookup_swap` simulation gap
