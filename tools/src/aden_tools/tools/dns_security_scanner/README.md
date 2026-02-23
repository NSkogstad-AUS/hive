# DNS Security Scanner

Performs non-intrusive DNS queries to evaluate a domain's email security configuration and DNS infrastructure hardening. Uses standard DNS lookups only — no active exploitation.

## Tools

### `dns_security_scan`

Scan a domain's DNS records for security issues.

| Parameter | Type   | Required | Description                                         |
| --------- | ------ | -------- | --------------------------------------------------- |
| `domain`  | string | Yes      | Domain to scan (e.g. `"example.com"`). No protocol prefix. |

**What it checks:**

| Check          | Description                                                   |
| -------------- | ------------------------------------------------------------- |
| **SPF**        | Sender Policy Framework record presence and policy strictness (`-all`, `~all`, `+all`) |
| **DMARC**      | DMARC record presence and enforcement level (`none`, `quarantine`, `reject`) |
| **DKIM**       | Probes common selectors (`default`, `google`, `selector1`, `selector2`, `mail`, etc.) |
| **DNSSEC**     | Checks for DNSKEY records indicating DNSSEC is enabled        |
| **MX records** | Lists mail exchange records                                   |
| **CAA records**| Lists Certificate Authority Authorization records             |
| **Zone transfer** | Tests if AXFR zone transfer is publicly allowed (critical misconfiguration) |

**Returns:** Results for each check, identified issues, and a `grade_input` dict compatible with the `risk_score` tool.

**Example:**

```python
dns_security_scan(domain="example.com")
```

**Example response (abbreviated):**

```json
{
  "domain": "example.com",
  "spf": {
    "present": true,
    "record": "v=spf1 include:_spf.google.com -all",
    "policy": "hardfail",
    "issues": []
  },
  "dmarc": {
    "present": true,
    "policy": "reject",
    "issues": []
  },
  "dkim": {
    "selectors_found": ["google"],
    "selectors_missing": ["default", "selector1"]
  },
  "dnssec": { "enabled": false, "issues": ["DNSSEC not enabled..."] },
  "zone_transfer": { "vulnerable": false },
  "grade_input": {
    "spf_present": true,
    "spf_strict": true,
    "dmarc_present": true,
    "dmarc_enforcing": true,
    "dkim_found": true,
    "dnssec_enabled": false,
    "zone_transfer_blocked": true
  }
}
```

## Credentials

No API key required. Uses standard DNS resolution.

## Dependencies

Requires `dnspython`:

```bash
uv pip install dnspython
```

## Integration with `risk_score`

Pass the `grade_input` field from the response directly to the `risk_score` tool to compute an overall security grade for the domain.

## Notes

- Strip the protocol (`https://`) before passing to `domain` — the tool handles this automatically but a clean domain is preferred.
- DKIM probing checks a fixed set of common selectors; custom selectors not in this list will not be detected.
- Zone transfer testing connects to each nameserver on TCP port 53 with a 5-second timeout.
