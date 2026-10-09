# Release Reports

This repository publishes transparency information for the releases of the Interoperability Test Bed (ITB) software
and its validators, produced by the European Commission's DIGIT Interoperable Europe Unit. For each release it provides:

- a **Software Bill of Materials (SBOM)** listing the components the release is built from, and
- a **Vulnerability Disclosure Report (VDR)** listing the known vulnerabilities that relate to those components,
  together with our assessment of whether, and how, each one affects the release.

Every file is cryptographically signed so that you can verify it is authentic and unmodified.

## Vulnerability status overview

For a quick answer to "does this release have known vulnerabilities?", see
**[VULNERABILITY_STATUS.md](VULNERABILITY_STATUS.md)**. It lists the published releases of each product, latest first,
and shows whether any of their vulnerabilities are assessed as `exploitable` (with links to the advisories), together
with the number of vulnerabilities assessed as not affecting the release. The same figures are available in
machine-readable form in `reports/index.json` (see below).

The overview is a convenience summary derived from the VDRs. The signed VDR of a release is always authoritative.

## Contents

```
VULNERABILITY_STATUS.md             Overview of the vulnerability status of all releases
cosign.pub                          Public key used to verify the signatures
reports/
├── index.json                      Machine-readable index of all published files
├── <product>/
│   └── <release>/
│       ├── <product>-<release>.bom.json                    SBOM
│       ├── <product>-<release>.bom.json.sigstore.json      Signature bundle for the SBOM
│       ├── <product>-<release>.vdr.json                    VDR
│       └── <product>-<release>.vdr.json.sigstore.json      Signature bundle for the VDR
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
bundle. It allows integrations to discover what is available without relying on directory listings, and it includes a
summary of the vulnerability status of each release so that a status check does not require downloading the VDRs:

```json
{
  "schemaVersion": 1,
  "products": {
    "<product>": {
      "name": "...",
      "latest": "<release>",
      "releases": {
        "<release>": {
          "bom": { "path": "...", "sha256": "...", "signature": { "path": "...", "sha256": "..." } },
          "vdr": { "path": "...", "sha256": "...", "signature": { "path": "...", "sha256": "..." } },
          "summary": {
            "exploitable": 0,
            "exploitableIds": [],
            "notAffected": 0,
            "falsePositive": 0,
            "resolved": 0,
            "assessmentInProgress": false
          }
        }
      }
    }
  }
}
```

- Paths are relative to the `reports/` folder.
- `latest` is the highest numbered (non-pre-release) release of the product.
- `summary` counts the distinct vulnerabilities in the release's VDR by assessment (`analysis.state`).
  `exploitableIds` lists the identifiers of those assessed as `exploitable`: a release is affected by known
  vulnerabilities when `exploitable` is greater than zero. `assessmentInProgress` is `true` while new findings for the
  release are being assessed, in which case the counts are those of the last published VDR.

## Machine-to-machine access

This repository is intended for people: browsing, reviewing and following the history of changes. For automated
integrations (for example a monitoring or vulnerability management tool that periodically reads VDRs), please use the
mirror of the `reports` folder published on the Interoperability Test Bed website instead of reading from GitHub:

```
https://www.itb.ec.europa.eu/release-reports/reports/
```

The mirror has exactly the same structure and file names as the `reports` folder in this repository, so the entry
point for discovery is:

```
https://www.itb.ec.europa.eu/release-reports/reports/index.json
```

and every `path` in the index is relative to that base URL. For example, the file listed under the path
`<product>/<release>/<product>-<release>.vdr.json` is available at:

```
https://www.itb.ec.europa.eu/release-reports/reports/<product>/<release>/<product>-<release>.vdr.json
```

Using the mirror is recommended for automated access because:

- it is not subject to the request rate limits that GitHub applies to automated clients,
- the address is maintained by the Test Bed and remains stable, even if this repository is moved or its hosting changes,
- it carries the same content as this repository. It is updated whenever the repository is, and the signature
  bundles are mirrored alongside the files, so everything can be [verified](#verifying-a-file) in the same way.

When polling, please check `index.json` and only download the files whose `sha256` digest has changed since your
last retrieval. If you only need to know whether a release is affected by known vulnerabilities, the `summary` of
the release in `index.json` is sufficient. Signatures are valid regardless of where a file was retrieved from.

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
through a trusted channel and compare the two. For this purpose feel free to contact the Test Bed team at DIGIT-ITB@ec.europa.eu.

## Feedback

If you believe a report is inaccurate, or you have a question about a vulnerability assessment, please open an issue
in this repository or email the Test Bed team at DIGIT-ITB@ec.europa.eu. Please do not use it to report new vulnerabilities in the software; use the project's security
reporting channels instead (see each project's `SECURITY.md` notice).
