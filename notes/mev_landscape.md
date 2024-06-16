# MEV Landscape on Solana

**Author:** Soubhagya | **Last updated:** 2026-01-20

## MEV Types

| Type | Mechanism | Impact |
|------|-----------|--------|
| Sandwich | Frontrun + backrun user swap | User receives worse price |
| Arbitrage | Cross DEX price discrepancy | Neutral to LPs, affects routing |
| Liquidation | Trigger undercollateralized positions | Borrower loses collateral |
| JIT Liquidity | Add LP before large swap, remove after | Extracts value from LPs |
| NFT snipe | Frontrun mint or listing | Collector overpays |

## Solana Specific Factors

- **No public mempool (default):** Validators see transactions via stake weighted QoS, reducing classic frontrunning but not eliminating it
- **Jito bundles:** Atomic multi transaction bundles with tip to block engine
- **Parallel execution:** Account lock ordering affects inclusion probability

## Detection

- Analyze transaction ordering within slot
- Compare execution price vs pool mid price at block start
- Identify repeated searcher wallets across DEX interactions

## Mitigations for Protocols

- Slippage tolerance enforcement
- TWAP oracles for price dependent logic
- Private mempool integration (Jito, bloXroute)
- Batch auctions for NFT mints

## Mitigations for Users

- Conservative slippage settings
- Smaller trade sizes
- Use limit orders where available
