# Mobile Wallet Security Research

**Author:** Soubhagya | **Last updated:** 2026-01-20

## Threat Surface

Mobile wallets face OS level adversaries unavailable to browser extensions: accessibility services, overlay attacks, keychain extraction on jailbroken devices, and app cloning.

## Secure Storage Architecture

```
Mnemonic → PBKDF2/Argon2 → AES key
AES key + vault data → Encrypted blob
Hardware KEK (Secure Enclave / StrongBox) wraps AES key
```

Seed never stored plaintext. Decryption only in isolated signing context after biometric/PIN.

## Platform Controls

| Control | iOS | Android |
|---------|-----|---------|
| Hardware backed keys | Secure Enclave | StrongBox / TEE |
| Jailbreak detection | File/syscall checks | RootBeer, Play Integrity |
| Overlay protection | Limited | filterTouchesWhenObscured |
| Screen capture block | isCaptured detection | FLAG_SECURE |

## Transaction Preview on Mobile

Smaller screen increases risk of users missing critical details. Requirements:
- Full recipient address on confirm screen
- Token mint verification against registry
- Simulation summary in plain language
- No transaction signing from push notifications or deep links without in app review

Cross reference: wallet_threat_matrix/mobile_threats.md
