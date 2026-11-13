# ITB-2026-006: Use of the operating system temporary folder for intermediate processing

- **Severity:** medium (CVSS 6.5)
- **CVSS vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:L/A:N`
- **Weakness:** [CWE-379](https://cwe.mitre.org/data/definitions/379.html)
- **Published:** 2026-11-13T00:00:00Z

## Description

The GITB Test Bed software and standalone validators use in certain occasions the OS temporary folder to store temporary files. The OS temporary folder is typically accessible to non-application OS users and can be accessed to reveal unintended data.

## Details

Use of the OS temporary folder occurs in certain processing paths in all standalone validators (XML, JSON, SHACL and CSV), as well as the GITB Test Bed software. The data written here is not sensitive in nature but it is not intended to be shared with users having access to the OS temporary folder.

## Recommendation

For the standalone XML, JSON, CSV and SHACL validators upgrade to release 1.13.1 or later.
For the GITB Test Bed software upgrade to release 1.30.2 or later.

As of these releases, all temporary processing files are stored in specific application-owned folders.

## Affected software

- `pkg:maven/com.gitb/GITB`: `vers:maven/<1.30.2`
- `pkg:maven/eu.europa.ec.itb.shacl/csvvalidator-war`: `vers:maven/<1.13.1`
- `pkg:maven/eu.europa.ec.itb.shacl/jsonvalidator-war`: `vers:maven/<1.13.1`
- `pkg:maven/eu.europa.ec.itb.shacl/shaclvalidator-war`: `vers:maven/<1.13.1`
- `pkg:maven/eu.europa.ec.itb.shacl/xmlvalidator-war`: `vers:maven/<1.13.1`

## References

- <https://cwe.mitre.org/data/definitions/379.html>

## Files

- Machine-readable advisory (CycloneDX): [ITB-2026-006.json](ITB-2026-006.json)
- Signature: [ITB-2026-006.json.sigstore.json](ITB-2026-006.json.sigstore.json)

Verify with `cosign verify-blob --key cosign.pub --bundle ITB-2026-006.json.sigstore.json ITB-2026-006.json` (see the
repository README for details).
