# ITB-2026-002: Server-Side Request Forgery (SSRF) and possible DoS through JSON-LD context resolution

- **Severity:** high (CVSS 8.3)
- **CVSS vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:C/C:L/I:L/A:L`
- **Weakness:** [CWE-918](https://cwe.mitre.org/data/definitions/918.html), [CWE-770](https://cwe.mitre.org/data/definitions/770.html)
- **Published:** 2026-11-13T00:00:00Z

## Description

The `context` provided in JSON-LD documents can be used to achieve an SSRF attacks when parsed. This allows an attacker providing a crafted JSON-LD document to achieve an SSRF attack, when used to probe internal and local (to the validator) addresses.

## Details

The `context` provided in JSON-LD documents is used to load additional information about the RDF content included in the document. It is provided as a URI to a remote context resource. RDF parsers, including the one included in the RDF/SHACL validator, read this context by default without applying restrictions. As such, an attacker can craft a JSON-LD document pointing to internal or local (to the validator) addresses, and achieve through the context resolution mechanism an SSRF attack. When pointing to an external resource, the attacker may also attempt an uncontrolled resource usage attack by pointing to large files or returning a slow response to starve the validator's thread pool.

## Recommendation

Upgrade to release 1.13.1 or later. As of release 1.13.1, the `context` of JSON-LD documents is strictly controlled as follows:
- Access to internal addresses are blocked unless explicitly whitelisted.
- Only HTTP and HTTPS protocols are allowed.
- References to remote resources are capped in terms of the resource's size.
- Connections to remote resources strictly timeout to avoid DoS via slow responses.

## Affected software

- `pkg:maven/eu.europa.ec.itb.shacl/shaclvalidator-war`: `vers:maven/<1.13.0`

## References

- <https://cwe.mitre.org/data/definitions/770.html>
- <https://cwe.mitre.org/data/definitions/918.html>

## Files

- Machine-readable advisory (CycloneDX): [ITB-2026-002.json](ITB-2026-002.json)
- Signature: [ITB-2026-002.json.sigstore.json](ITB-2026-002.json.sigstore.json)

Verify with `cosign verify-blob --key cosign.pub --bundle ITB-2026-002.json.sigstore.json ITB-2026-002.json` (see the
repository README for details).
