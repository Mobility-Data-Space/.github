# `trivy-fs-scan`

Shared composite action that runs a [Trivy](https://trivy.dev) **filesystem**
scan. It:

1. Runs a report scan (`MEDIUM,HIGH,CRITICAL`) and uploads it as an artifact.
2. Runs a second scan that fails the build on `HIGH,CRITICAL` findings.

## Usage

```yaml
jobs:
  filesystem-scan:
    name: Trivy Filesystem & Secrets Scan
    runs-on: ubuntu-latest
    permissions:
      contents: read
    steps:
      - uses: actions/checkout@v5
      - uses: Mobility-Data-Space/.github/actions/trivy-fs-scan@main
```

That single step replaces the Trivy report/fail steps previously duplicated in
each repo.

## Inputs

| Input                 | Default                                    | Description                                                              |
|-----------------------|--------------------------------------------|--------------------------------------------------------------------------|
| `scanners`            | `vuln,secret`                              | Comma-separated Trivy scanners to run.                                   |
| `report-severity`     | `MEDIUM,HIGH,CRITICAL`                     | Severities included in the (non-failing) report scan.                    |
| `fail-severity`       | `HIGH,CRITICAL`                            | Severities that cause the failing scan to exit non-zero.                 |
| `ignore-unfixed`      | `true`                                     | Whether the failing scan ignores vulnerabilities with no fix available. |
| `trivyignore`         | `.trivyignore`                             | Path to a Trivy ignore file applied to the failing scan.                 |
| `report-name`         | `trivy-fs`                                 | Name of the uploaded report artifact.                                    |
| `output-file`         | `trivy-fs.txt`                             | File the report scan writes to and uploads.                              |

## Pinned versions

Third-party actions are pinned to commit SHAs. When bumping, update both the SHA
and its trailing `# vX.Y.Z` comment.
