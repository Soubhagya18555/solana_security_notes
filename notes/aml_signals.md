# AML Signals and Blockchain Analytics

**Author:** Soubhagya | **Last updated:** 2026-01-20

## On Chain Risk Indicators

| Signal | Risk Level | Description |
|--------|------------|-------------|
| Mixer interaction | High | Funds passed through Tornado or similar |
| CEX deposit within 1h of theft | High | Rapid cashout pattern |
| Known drainer program | Critical | Interaction with flagged program ID |
| Fresh wallet chain | Medium | Sequential new wallets moving funds |
| High velocity | Medium | Rapid split and consolidate pattern |
| Sanctioned address | Critical | OFAC listed wallet interaction |

## Clustering Methodology

1. **Co spending:** Addresses appearing as signers in same transaction
2. **Funding tree:** Trace SOL funding source backward
3. **Temporal correlation:** Transactions within tight time window
4. **Behavioral fingerprint:** Shared program interactions, token preferences

## Compliance Integration

Wallets and protocols may integrate risk scoring before:
- Large withdrawals
- Bridge interactions
- Fiat off ramps

False positive handling: allow user appeal, manual review queue, graduated friction (delay vs block).

## Privacy Balance

Over aggressive AML harms legitimate privacy seeking users. Transparent scoring criteria and minimal data retention recommended.
