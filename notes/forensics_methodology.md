# On Chain Forensics Methodology

**Author:** Soubhagya | **Last updated:** 2026-01-20

## Investigation Framework

### Phase 1: Triage
- Identify victim wallet(s) and time window
- Calculate total loss by token and USD value at time of theft
- Collect initial transaction signatures from victim report or block explorer

### Phase 2: Transaction Decomposition
- Fetch full transaction with `getTransaction` (version: max)
- Parse outer and inner instructions
- Identify program IDs, signers, and account roles
- Map CPI tree for hidden transfers

### Phase 3: Fund Flow Tracing
- Follow outbound transfers from victim
- Track through DEX swaps, bridge deposits, mixer interactions
- Label known entities (CEX hot wallets, mixer contracts, drainer kits)
- Build graph with temporal ordering

### Phase 4: Attribution
- Cluster addresses by: funding source, timing, shared program interactions
- Cross reference with threat intel feeds
- Identify drainer kit signatures (known program deployments)

### Phase 5: Reporting
- Timeline of events
- Fund flow diagram
- Attribution confidence level
- Recommended actions (CEX freeze request, law enforcement referral)

## Tools

| Tool | Purpose |
|------|---------|
| Solana FM / Solscan | Transaction visualization |
| Helius / Shyft APIs | Enhanced transaction parsing |
| Arkham / Nansen | Entity labeling |
| Custom Python (solders) | Batch analysis |

## Evidence Preservation

- Archive raw RPC responses (transactions, account states)
- Screenshot explorer views with timestamps
- Hash all collected data for chain of custody
