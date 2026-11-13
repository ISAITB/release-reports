# ITB-2026-004: Uncontrolled remote resource lookups in validation arteefacts

- **Severity:** medium (CVSS 4.3)
- **CVSS vector:** `CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:N/I:N/A:L`
- **Weakness:** [CWE-770](https://cwe.mitre.org/data/definitions/770.html)
- **Published:** 2026-11-13T00:00:00Z

## Description

Validation artefacts (XSDs, Schematron, SHACL shapes, JSON Schemas) may refer to remote resources. When parsing looks up these resources, there are no controls applied, meaning that a crafted validation artefact can be used to abuse system resources by returning large content or a slow response.

## Details

Remote lookups in validation artefacts require a crafted artefact be used to achieve an exploit. This affects:
- The standalone JSON validator for JSON Schemas, referring to a remote schema.
- The standalone XML validator for XML Schemas and Schematron, including or importing remote resources.
- The standalone RDF/SHACL validator for SHACL shapes, through OWL imports or a JSON-LD context.
- The GITB Test Bed's test engine as described above, in relation to the `JsonValidator`, `XmlValidator`, `SchematronValidator` and `ShaclValidator` built-in handlers.

An attacker may use these to point to a resource that is either excessively large for a disk usage or bandwidth saturation, or a slow responding resource to block processing threads. Nonetheless, a successful exploit would require crafting validation artefacts which is reserved to an administrator (the person configuring a validator or defining test cases).

## Recommendation

For a standalone XML, JSON or SHACL validator upgrade to release 1.13.1 or later.
For the GITB Test Bed software upgrade to release 1.30.2 or later.

As of these releases, resource lookups are strictly controlled as follows:
- Access to internal addresses are blocked unless explicitly whitelisted.
- Only HTTP and HTTPS protocols are allowed.
- References to remote resources are capped in terms of the resource's size.
- Connections to remote resources strictly timeout to avoid DoS via slow responses.

## Affected software

- `pkg:maven/com.gitb/GITB`: `vers:maven/<1.30.2`
- `pkg:maven/eu.europa.ec.itb.shacl/jsonvalidator-war`: `vers:maven/<1.13.1`
- `pkg:maven/eu.europa.ec.itb.shacl/shaclvalidator-war`: `vers:maven/<1.13.1`
- `pkg:maven/eu.europa.ec.itb.shacl/xmlvalidator-war`: `vers:maven/<1.13.1`

## References

- <https://cwe.mitre.org/data/definitions/770.html>

## Files

- Machine-readable advisory (CycloneDX): [ITB-2026-004.json](ITB-2026-004.json)
- Signature: [ITB-2026-004.json.sigstore.json](ITB-2026-004.json.sigstore.json)

Verify with `cosign verify-blob --key cosign.pub --bundle ITB-2026-004.json.sigstore.json ITB-2026-004.json` (see the
repository README for details).
