# ZK Compression and State Compression Security

**Author:** Soubhagya | **Last updated:** 2026-01-20

## Overview

Solana state compression uses zero knowledge proofs to store account data in Merkle trees with only root hash on chain. Dramatically reduces rent cost but shifts trust to indexer infrastructure.

## Security Considerations

### Indexer Trust
Compressed account state retrieved from indexer (Helius, etc.). Malicious indexer returns false state enabling double spend of compressed NFTs or tokens.

**Mitigation:** Verify Merkle proofs on chain or client side against known root.

### Proof Verification Bugs
ZK circuit implementation errors could allow invalid state transitions. Audit proof system separately from application logic.

### Root Update Authority
Who can update Merkle tree root? Compromised authority rewrites history.

### Decompression Attacks
Force decompression of compressed state to exhaust account data limits or compute budget.

## Wallet Implications

- Display compressed vs uncompressed asset distinction
- Verify proof when receiving compressed assets
- Understand indexer dependency for balance display
