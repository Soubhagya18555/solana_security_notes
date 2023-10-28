# Android Wallet IPC Security

**Author:** Soubhagya | **Last updated:** 2026-01-28

## IPC surface map

| Mechanism | Data exposed | Risk |
|-----------|--------------|------|
| Explicit intents | Transaction bytes, deep links | Intent redirection |
| ContentProvider | Vault metadata if exported | Data leak |
| AIDL/Binder services | Signing API | Privilege escalation |
| SharedUserId (legacy) | Shared sandbox | Supply chain |
| Clipboard | Addresses, seeds | Cross app read |

## Intent hardening

```xml
<!-- AndroidManifest.xml -->
<activity
    android:name=".SignTransactionActivity"
    android:exported="false" />
```

For verified App Links entry only:

```xml
<activity android:name=".DeepLinkActivity" android:exported="true">
    <intent-filter android:autoVerify="true">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="https" android:host="wallet.example.com" />
    </intent-filter>
</activity>
```

Validate caller:

```kotlin
fun verifyCaller(): Boolean {
    val caller = callingPackage ?: return false
    val expected = setOf("com.trusted.dapp") // partner allowlist
    if (caller !in expected) return false
    val info = packageManager.getPackageInfo(caller, GET_SIGNING_CERTIFICATES)
    return info.signingInfo.apkContentsSigners.any { cert ->
        cert.toCharsString() == TRUSTED_CERT_SHA256
    }
}
```

## WebView isolation

- No `addJavascriptInterface` on untrusted URLs
- Use Custom Tabs for arbitrary dApp origins
- Clear WebView storage on logout

## Keystore integration

```kotlin
KeyGenParameterSpec.Builder(
    "wallet_master",
    KeyProperties.PURPOSE_ENCRYPT or KeyProperties.PURPOSE_DECRYPT
)
    .setBlockModes(KeyProperties.BLOCK_MODE_GCM)
    .setEncryptionPaddings(KeyProperties.ENCRYPTION_PADDING_NONE)
    .setUserAuthenticationRequired(true)
    .setInvalidatedByBiometricEnrollment(true)
    .build()
```

## Overlay and tapjacking

- `filterTouchesWhenObscured = true`
- Detect `Settings.canDrawOverlays()` during signing
- Enumerate accessibility services (WTM_012)

## Backup exfiltration

```xml
<application android:allowBackup="false" android:fullBackupContent="false">
```

## Root detection signals

| Signal | Method |
|--------|--------|
| su binary | File path check native |
| Magisk | Package name scan |
| Play Integrity | API call MEETS_DEVICE_INTEGRITY |
| Debugger | Debug.isDebuggerConnected |

Degrade to view only on positive combined score, not single signal false positive.

## Testing tools

- MobSF static analysis
- Frida dynamic hook tests
- Intent fuzzing with `adb shell am start`

## Related

- wallet_threat_matrix: WTM_036, WTM_037, WTM_048, catalog/android_threats.md
- key_management.md
