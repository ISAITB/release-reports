# Release Reports

This repository publishes transparency information for the releases of the Interoperability Test Bed (ITB) software
and its validators, produced by the European Commission's DIGIT Interoperable Europe Unit. For each release it provides:

- a **Software Bill of Materials (SBOM)** listing the components the release is built from, and
- a **Vulnerability Disclosure Report (VDR)** listing the known vulnerabilities that relate to those components,
  together with our assessment of whether, and how, each one affects the release.

Every file is cryptographically signed so that you can verify it is authentic and unmodified.

## Contents

```
reports/
├── index.json                      Machine-readable index of all published files
├── <product>/
│   └── <release>/
│       ├── <product>-<release>.bom.json                    SBOM
│       ├── <product>-<release>.bom.json.sigstore.json      Signature bundle for the SBOM
│       ├── <product>-<release>.vdr.json                    VDR
│       └── <product>-<release>.vdr.json.sigstore.json      Signature bundle for the VDR
cosign.pub                          Public key used to verify the signatures
```

| Product folder     | Software                          |
|--------------------|-----------------------------------|
| `itb`              | Interoperability Test Bed         |
| `xml-validator`    | XML validator                     |
| `json-validator`   | JSON validator                    |
| `csv-validator`    | CSV validator                     |
| `shacl-validator`  | SHACL validator                   |

The `<release>` folder normally matches the release number of the software (e.g. `1.2.3`). Reports for
branch-specific or pre-release builds may occasionally be published under their own identifier.

## File formats

All reports are [CycloneDX](https://cyclonedx.org/) 1.5 documents in JSON format.

- **SBOM (`*.bom.json`)**: the component inventory of the release. It is not expected to change after publication,
  other than to correct errors.
- **VDR (`*.vdr.json`)**: the components that are affected by known vulnerabilities, and for each vulnerability the
  assessment (`analysis`) made by the maintainers. The VDR can also be used as a VEX document, as it states whether a
  vulnerability that is present in a component actually affects the product. The `analysis.state` is one of:

  | State            | Meaning                                                                                  |
  |------------------|------------------------------------------------------------------------------------------|
  | `not_affected`   | The release is not affected. `justification` and `detail` explain why.                   |
  | `exploitable`    | The release is affected. `detail` provides guidance (typically to upgrade).              |
  | `resolved`       | The vulnerability has been addressed in this release.                                    |
  | `false_positive` | The vulnerability does not apply to the identified component.                            |

Reports reference components by their `bom-ref`. A VDR is always published together with the SBOM it was produced
against, so these references resolve within the same release folder.

## Updates to published reports

VDRs are living documents. New vulnerabilities are disclosed continuously, and our assessments are reviewed over
time, so the VDR of an already released version may be updated after its release. Updates are recorded in this
repository's commit history. Files are only updated when their content (as opposed to merely their generation time)
has changed; the timestamp inside a file therefore tells you when its content was last changed.

## Machine-readable index

`reports/index.json` lists every product, release and file, with a SHA-256 digest of each file and its signature
bundle. It allows integrations to discover what is available without relying on directory listings:

```json
{
  "schemaVersion": 1,
  "products": {
    "<product>": {
      "releases": {
        "<release>": {
          "bom": { "path": "...", "sha256": "...", "signature": { "path": "...", "sha256": "..." } },
          "vdr": { "path": "...", "sha256": "...", "signature": { "path": "...", "sha256": "..." } }
        }
      }
    }
  }
}
```

Paths are relative to the `reports/` folder.

## Verifying a file

The files are signed with [Sigstore cosign](https://docs.sigstore.dev/cosign/overview/), and each signature is
recorded in the public Rekor transparency log. To verify a file using [cosign](https://docs.sigstore.dev/cosign/system_config/installation/)
(v2.x users should add `--new-bundle-format`):

```
cosign verify-blob \
  --key cosign.pub \
  --bundle <product>-<release>.vdr.json.sigstore.json \
  <product>-<release>.vdr.json
```

A successful check prints `Verified OK`. Verification also confirms that the signature was logged in Rekor, which
proves that the file existed, in that form, at the time of signing.

Because the public key is delivered through this same repository, you may wish to obtain a copy of `cosign.pub`
through a second independent channel and compare the two.

## Feedback

If you believe a report is inaccurate, or you have a question about a vulnerability assessment, please open an issue
in this repository. Please do not use it to report new vulnerabilities in the software; use the project's security
reporting channels instead.
