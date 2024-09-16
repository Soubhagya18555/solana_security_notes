# Oracle Manipulation Attacks

**Author:** Soubhagya | **Last updated:** 2026-01-20

## Oracle Types on Solana

| Oracle | Mechanism | Manipulation Cost |
|--------|-----------|-------------------|
| Pyth | Pull model, price accounts | Medium (stale price) |
| Switchboard | Aggregator accounts | Medium |
| On chain TWAP | Time weighted pool price | High (sustained manipulation) |
| Spot price | Single pool reserve ratio | Low (flash loan) |

## Flash Loan Attack Pattern

1. Borrow large amount from lending protocol
2. Execute swap moving pool price
3. Trigger protocol action depending on manipulated price (liquidation, mint, borrow)
4. Reverse swap and repay loan
5. Profit from price dependent action

## Defenses

- **TWAP:** Time weighted average over multiple blocks
- **Staleness check:** Reject prices older than threshold
- **Deviation check:** Reject prices deviating from secondary source
- **Minimum liquidity:** Require minimum pool depth for price validity
- **Circuit breakers:** Pause on extreme price movement

## Case Study Pattern

Lending protocol uses spot DEX price for collateral valuation. Attacker flash loans to crash collateral price, triggers liquidation of healthy positions, acquires collateral at discount.
