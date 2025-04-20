# Solana Security Notes

**Author:** Soubhagya  
**Type:** Living research repository  
**Focus:** Solana VM security, on chain forensics, program auditing

A continuously updated collection of deep technical research notes on Solana security. Each document covers attack vectors, defensive patterns, and forensic techniques relevant to security engineers, auditors, and protocol developers.

## Research Index

| Note | Topic | Key Areas |
|------|-------|-----------|
| [svm_internals.md](notes/svm_internals.md) | SVM architecture | eBPF execution, account model, compute budget, syscalls |
| [cpi_security.md](notes/cpi_security.md) | Cross program invocation | Reentrancy, signer leakage, arbitrary CPI |
| [token_2022_hooks.md](notes/token_2022_hooks.md) | Token 2022 extensions | Transfer hooks, fees, confidential transfers |
| [mev_landscape.md](notes/mev_landscape.md) | MEV on Solana | Sandwich, JIT liquidity, Jito bundles |
| [forensics_methodology.md](notes/forensics_methodology.md) | On chain forensics | Fund tracing, attribution, evidence preservation |
| [versioned_tx_security.md](notes/versioned_tx_security.md) | Versioned transactions | Address lookup table attacks, v0 parsing |
| [pda_attacks.md](notes/pda_attacks.md) | PDA security | Seed collision, bump validation, ownership |
| [oracle_manipulation.md](notes/oracle_manipulation.md) | Oracle attacks | Flash loans, TWAP, staleness checks |
| [governance_attacks.md](notes/governance_attacks.md) | Governance risks | Flash loan voting, timelock bypass |
| [mobile_wallet_security.md](notes/mobile_wallet_security.md) | Mobile wallets | Secure enclave, overlay attacks, deep links |
| [zk_compression.md](notes/zk_compression.md) | ZK compression | Indexer trust, proof verification |
| [supply_chain_security.md](notes/supply_chain_security.md) | Supply chain | npm poisoning, CI/CD, verified builds |
| [incident_response.md](notes/incident_response.md) | Incident response | SEV classification, playbooks, communication |
| [aml_signals.md](notes/aml_signals.md) | AML analytics | Risk indicators, clustering, compliance |
| [program_upgrade_risks.md](notes/program_upgrade_risks.md) | Program upgrades | Authority models, malicious upgrade scenarios |

## How to Use

1. Start with [svm_internals.md](notes/svm_internals.md) for foundational SVM security concepts.
2. Reference specific notes during audit engagements or threat modeling sessions.
3. Cross link with [solana_common_vulnerabilities](../solana_common_vulnerabilities) for standardized vulnerability IDs.
4. Pair with [wallet_threat_matrix](../wallet_threat_matrix) for wallet specific threat context.

## Related Repositories

| Repository | Relationship |
|------------|--------------|
| solana_common_vulnerabilities | OWASP style vulnerability catalog (SCV IDs) |
| wallet_threat_matrix | Wallet threat catalog (WTM IDs) |
| web3_threat_model_framework | STRIDE/PASTA threat modeling methodology |

## Contributing

Submit pull requests with new research notes or updates to existing documents. Maintain technical depth: include code examples, attack patterns, and concrete mitigations.

## Disclaimer

These notes represent security research for educational purposes. Always verify findings against current Solana runtime versions and conduct professional audits before mainnet deployment.

## License

MIT License
