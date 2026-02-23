# SSL/TLS Scanner

Analyzes a host's SSL/TLS configuration and certificate. Checks protocol version, cipher suite strength, certificate validity, expiry, and common misconfigurations. Uses Python stdlib only (`ssl` + `socket`) — no external dependencies.

## Tools

### `ssl_tls_scan`

Scan a host's SSL/TLS setup.

| Parameter  | Type    | Required | Description                                                                   |
| ---------- | ------- | -------- | ----------------------------------------------------------------------------- |
| `hostname` | string  | Yes      | Domain to scan (e.g. `"example.com"`). Protocol prefix is stripped automatically. |
| `port`     | integer | No       | Port to connect to (default: `443`)                                           |

**What it checks:**

| Check                  | Severity if failing | Description                                              |
| ---------------------- | ------------------- | -------------------------------------------------------- |
| TLS version            | High                | Flags TLSv1.0, TLSv1.1, SSLv2, SSLv3 as insecure        |
| Cipher suite           | High                | Flags RC4, DES, 3DES, MD5, NULL, EXPORT, anon ciphers    |
| Cipher key length      | High                | Flags ciphers with fewer than 128-bit keys               |
| Certificate validity   | Critical            | Checks certificate is trusted and not expired            |
| Certificate expiry     | Medium              | Warns if certificate expires within 30 days              |
| Self-signed            | High                | Flags certificates where subject == issuer               |

**Returns:** TLS version, cipher name and bit length, full certificate details (subject, issuer, SAN, expiry, SHA-256 fingerprint), list of issues with remediation, and a `grade_input` dict compatible with the `risk_score` tool.

**Example:**

```python
ssl_tls_scan(hostname="example.com")
ssl_tls_scan(hostname="example.com", port=8443)
```

**Example response (abbreviated):**

```json
{
  "hostname": "example.com",
  "port": 443,
  "tls_version": "TLSv1.3",
  "cipher": "TLS_AES_256_GCM_SHA384",
  "cipher_bits": 256,
  "certificate": {
    "subject": "CN=example.com",
    "issuer": "CN=R11, O=Let's Encrypt, C=US",
    "not_before": "2025-01-01T00:00:00+00:00",
    "not_after": "2025-04-01T00:00:00+00:00",
    "days_until_expiry": 37,
    "san": ["example.com", "www.example.com"],
    "self_signed": false,
    "sha256_fingerprint": "abc123..."
  },
  "issues": [],
  "grade_input": {
    "tls_version_ok": true,
    "cert_valid": true,
    "cert_expiring_soon": false,
    "strong_cipher": true,
    "self_signed": false
  }
}
```

## Credentials

No API key required. Uses Python's `ssl` and `socket` stdlib.

## Integration with `risk_score`

Pass the full JSON output to the `risk_score` tool as `ssl_results` to include TLS configuration in the overall security grade.

## Notes

- Connection timeout is 10 seconds.
- If certificate verification fails, the scanner retries with verification disabled to still collect certificate details, and reports the verification failure as a critical issue.
- Only use against hosts you own or have explicit written authorisation to scan.
