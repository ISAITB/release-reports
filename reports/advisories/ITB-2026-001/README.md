# ITB-2026-001: Potential Server-Side Request Forgery (SSRF) through user-provided URI retrieval

| | |
|---|---|
| Severity | medium (CVSS 5.8) |
| CVSS vector | `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:C/C:L/I:N/A:N` |
| Weakness | [CWE-918](https://cwe.mitre.org/data/definitions/918.html), [CWE-1356](https://cwe.mitre.org/data/definitions/1356.html) |
| Published | 2026-11-06T00:00:00Z |

## Description

The GITB specifications foresee the possibility for users and clients to provide inputs as URIs of the remote resources to be loaded (e.g. referring to a remote document to validate). The validator allows inputs as URIs by default, but also does not apply restrictions on the referenced resources. This allows an attacker to abuse this feature to achieve an SSRF attack, when used to probe internal and local (to the validator) addresses.

## Details

GITB-compliant validators provide functionality to retrieve resources identified by user-provided URIs. This introduces a server-side request capability that may be abused to perform Server-Side Request Forgery (SSRF) attacks.

An attacker may attempt to provide URIs targeting internal network addresses, localhost, link-local addresses or other restricted destinations. Depending on the effectiveness of destination validation, DNS resolution handling and redirect processing, this could allow the application to make requests to resources that would otherwise be inaccessible to the attacker. The potential impact includes internal network resource discovery, service probing and, depending on the accessible resources and response handling, unauthorised retrieval of internal information.

The vulnerability affects the RDF, JSON, CSV and XML validators, prior to release 1.11.2.

## Recommendation

Upgrade to release 1.11.2 or later.

As of release 1.11.2 the validators:
- Block access to internal addresses, unless for explicitly whitelisted paths.
- Disable by default URI input as an option on all exposed APIs.

## Affected packages

- `pkg:maven/eu.europa.ec.itb.csv/csvvalidator-war`: `vers:maven/<1.11.2`
- `pkg:maven/eu.europa.ec.itb.json/jsonvalidator-war`: `vers:maven/<1.11.2`
- `pkg:maven/eu.europa.ec.itb.shacl/shaclvalidator-war`: `vers:maven/<1.11.2`
- `pkg:maven/eu.europa.ec.itb.xml/xmlvalidator-war`: `vers:maven/<1.11.2`

## Affected releases

Assessment of the published releases, as recorded in their Vulnerability Disclosure Reports (VDRs):

| Product | Release | Assessment |
|---|---|---|
| CSV validator | 1.11.0 | `exploitable` |
| CSV validator | 1.11.1 | `exploitable` |
| JSON validator | 1.11.0 | `exploitable` |
| JSON validator | 1.11.1 | `exploitable` |
| SHACL validator | 1.11.0 | `exploitable` |
| SHACL validator | 1.11.1 | `exploitable` |
| XML validator | 1.11.0 | `exploitable` |
| XML validator | 1.11.1 | `exploitable` |

## References

- <https://cwe.mitre.org/data/definitions/918.html>
- <https://owasp.org/www-community/attacks/Server_Side_Request_Forgery>

## Files

- Machine-readable advisory (CycloneDX): [ITB-2026-001.json](ITB-2026-001.json)
- Signature: [ITB-2026-001.json.sigstore.json](ITB-2026-001.json.sigstore.json)

Verify with `cosign verify-blob --key cosign.pub --bundle ITB-2026-001.json.sigstore.json ITB-2026-001.json` (see the
repository README for details).
