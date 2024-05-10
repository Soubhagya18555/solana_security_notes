# Incident Response for Solana Protocols

**Author:** Soubhagya | **Last updated:** 2026-01-20

## Severity Classification

| Level | Criteria | Response Time |
|-------|----------|---------------|
| SEV1 | Active exploit, funds at risk | Immediate (< 15 min) |
| SEV2 | Vulnerability disclosed, not yet exploited | < 4 hours |
| SEV3 | Suspicious activity, unconfirmed | < 24 hours |
| SEV4 | Minor issue, no fund risk | Next business day |

## SEV1 Playbook

1. **Detect:** Monitoring alert or community report
2. **Assess:** Confirm exploit via transaction analysis
3. **Contain:** Pause protocol if pause mechanism exists
4. **Communicate:** Status page, Twitter, Discord pinned message
5. **Investigate:** Fund flow tracing (see forensics_methodology.md)
6. **Remediate:** Deploy fix via upgrade authority
7. **Post mortem:** Public report within 72 hours

## Communication Templates

- Initial: `We are investigating reports of [issue]. User funds [status]. Do not interact with [contract/program] until further notice.`
- Resolution: `Exploit contained. [Amount] affected. Remediation deployed at [tx sig]. Post mortem forthcoming.`

## Key Contacts

Maintain encrypted contact list: core engineers, multisig signers, legal counsel, audit firm, ecosystem partners (RPC providers, explorers).

## Evidence Collection

Preserve: transaction signatures, program logs, account state snapshots, deployment artifacts, internal communication timestamps.
