# Supply Chain Security for Solana Projects

**Author:** Soubhagya | **Last updated:** 2026-01-20

## Attack Vectors

### npm Crate Poisoning
Malicious `@solana/web3.js` typosquat or compromised maintainer account injects key exfiltration into popular packages.

### Anchor Version Pinning
Different Anchor versions produce different program bytecode. Unpinned builds may include unintended changes.

### Program Deployment Pipeline
Compromised CI/CD deploys malicious program binary to mainnet upgrade authority.

### Verified Build Gaps
Solana verified builds reproducibility not universal. Users cannot confirm deployed bytecode matches source.

## Defenses

| Layer | Control |
|-------|---------|
| Dependencies | Lock files, hash verification, Dependabot |
| Build | Reproducible builds, Docker pinned images |
| Deploy | Multisig upgrade authority, hardware keys |
| Verify | Publish source hash, verified build badge |
| Monitor | Runtime program hash monitoring |

## Incident Examples (Patterns)

- Event listener dependency with embedded telemetry exfiltrating wallet addresses
- GitHub Actions secret exfiltration via malicious PR workflow
- SBF toolchain substitution in CI environment
