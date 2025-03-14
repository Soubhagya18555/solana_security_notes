# Web Wallet XSS and Frontend Injection

**Author:** Soubhagya | **Last updated:** 2026-01-28

## Scope

Web wallets (browser extensions and standalone web apps) render untrusted data from chain, IPFS, and dApp origins. XSS enables transaction substitution, seed exfiltration, and provider hijacking.

## XSS sources in Web3

| Source | Vector | Example |
|--------|--------|---------|
| Token name/symbol | Metadata JSON | `<img onerror=...>` |
| NFT description | Off chain uri HTML | `<script>` in description |
| dApp innerHTML | Partner integration | Wallet adapter DOM injection |
| URL query params | Reflected display | `?error=<script>` |
| RPC error messages | Rendered in UI | Unescaped error string |

## Content Security Policy baseline

```
Content-Security-Policy:
  default-src 'self';
  script-src 'self';
  object-src 'none';
  base-uri 'self';
  frame-ancestors 'none';
  connect-src 'self' https://*.solana.com wss://...
```

Avoid `'unsafe-inline'` in production extension pages.

## Safe rendering rules

```tsx
// Correct: plain text
<div>{tokenName}</div>

// Dangerous: HTML injection
<div dangerouslySetInnerHTML={{ __html: nftDescription }} />
```

Use DOMPurify with strict allowlist if rich text required (generally avoid for wallets).

## Extension specific risks

### Content script isolation

Content scripts share DOM with page but separate JS heap. Messaging bridge must validate origin on every `postMessage`.

```javascript
chrome.runtime.onMessage.addListener((msg, sender, respond) => {
  if (sender.origin !== expectedOrigin) return;
});
```

### Provider injection (WTM_021)

Inject provider at `document_start` before page scripts. Page cannot win race if injection order guaranteed.

## Transaction preview integrity

Build preview from locally deserialized transaction bytes, not dApp supplied JSON description.

## Supply chain

- Lock npm dependencies
- Reproducible build hashes published
- Subresource integrity on CDN hosted bundles

## NFT gallery XSS (WTM_051)

Fetch metadata with:

- `Accept: application/json` only
- Reject `text/html` content type
- Sandbox fetch in isolated worker without DOM access

## Detection during QA

- XSS payload list on token name fields in devnet mint tests
- OWASP ZAP scan on wallet web UI
- Extension permission audit (broad host permissions)

## Incident response

If XSS confirmed in production:

1. Disable affected render path via remote config (signed)
2. Publish extension emergency update
3. Analyze on chain txs from affected users for drain patterns

## Related

- wallet_threat_matrix: WTM_010, WTM_021, WTM_034, WTM_051
- web3_threat_model_framework: CHECKLIST_FRONTEND.md
