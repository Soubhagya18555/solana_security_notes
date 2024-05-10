# Key Management for Solana Wallets

**Author:** Soubhagya | **Last updated:** 2026-01-28

## Key hierarchy

```
BIP39 mnemonic (128-256 bit entropy)
    └── SLIP-0010 master seed
        └── m/44'/501'/account'/0'  (ed25519 hardened paths)
            └── Signing keypair
```

## Storage tiers

| Tier | Location | Use case |
|------|----------|----------|
| Hot | Mobile keystore / extension encrypted vault | Daily transactions |
| Warm | Hardware wallet secure element | High value signing |
| Cold | Paper / steel backup | Disaster recovery |
| Operational | HSM / multisig | Protocol admin |

## Encryption at rest

Mobile and extension wallets should use:

- Argon2id or scrypt with OWASP 2023 minimum parameters
- Unique salt per vault
- Key encryption key in platform secure enclave (Android Keystore, iOS Secure Enclave)

Never store mnemonic plaintext in SQLite, SharedPreferences, or localStorage.

## Memory hygiene

- Zeroize seed byte arrays after derivation
- Avoid logging key material in debug builds shipped to stores
- Scrub crash reports (WTM_014)

## Backup UX risks

| Action | Risk | Mitigation |
|--------|------|------------|
| Screenshot seed | Permanent image leak | Block screenshots on seed screen |
| Cloud backup | iCloud/Google drive exposure | Device only keystore flag |
| Clipboard copy | Third party app read | Auto clear after 30s |
| Email backup | Account compromise | Never offer email backup |

## Multisig and MPC

On chain multisig (Squads) separates keys across parties. MPC wallets split key shares without on chain enrollment. Threat model shifts to collusion and recovery ceremonies.

## Protocol admin keys

- Separate upgrade authority from treasury authority
- Hardware wallet required for mainnet deploy
- Public timelock calendar for upgrade transactions

## Rotation procedures

1. Generate new key on clean device
2. Transfer assets on chain
3. Revoke old delegate approvals
4. Update documentation and monitoring addresses
5. Secure wipe old device

## Compliance considerations

Export controlled cryptography notice for international distribution. No key escrow for non custodial products.

## Related

- wallet_threat_matrix WTM_003, WTM_009, WTM_014, WTM_043, WTM_047, WTM_052
- android_wallet_ipc.md for platform specifics
