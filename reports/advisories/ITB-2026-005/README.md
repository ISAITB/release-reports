# ITB-2026-005: XML External Entity (XXE) through data archive import

- **Severity:** low (CVSS 2.7)
- **CVSS vector:** `CVSS:3.1/AV:N/AC:L/PR:H/UI:N/S:U/C:L/I:N/A:N`
- **Weakness:** [CWE-611](https://cwe.mitre.org/data/definitions/611.html)
- **Published:** 2026-11-13T00:00:00Z

## Description

When the GITB Test Bed software imports a data archive, it parses its metadata to determine the release from which it was made. The parser used for this processing allows external entity definitions, enabling an XML External Entity (XXE) attack.

## Details

An attacker that can craft a data archive, can include external entity definitions within it that will lead to a successful XML External Entity (XXE) attack. Exploiting this however requires elevated access, as only administrators can trigger such imports through the user interface. Alternatively, this could also be achieved by an operations staff member that configures a data archive ingestion by specific deployment configuration.

## Recommendation

Upgrade to release 1.30.3 or later. As of release 1.30.3, the GITB Test Bed software uses the same hardened XML parser for this data archive processing step, as used wherever else XML gets parsed.

## Affected software

- `pkg:maven/com.gitb/GITB`: `vers:maven/>=1.9.0|<1.30.3`

## References

- <https://cwe.mitre.org/data/definitions/611.html>

## Files

- Machine-readable advisory (CycloneDX): [ITB-2026-005.json](ITB-2026-005.json)
- Signature: [ITB-2026-005.json.sigstore.json](ITB-2026-005.json.sigstore.json)

Verify with `cosign verify-blob --key cosign.pub --bundle ITB-2026-005.json.sigstore.json ITB-2026-005.json` (see the
repository README for details).
