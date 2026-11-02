# `trivy-image-scan`

Shared composite action that runs a [Trivy](https://trivy.dev) vulnerability
scan against a container **image**. It:

1. Runs a report scan (`MEDIUM,HIGH,CRITICAL`) and uploads it as an artifact.
2. Runs a second scan that fails the build on `HIGH,CRITICAL` findings.

The image must already be built/loaded (or pushed) before this action runs —
building and publishing stays in each repo's own workflow/action. This action
only scans the given `image-ref`.

## Usage

```yaml
steps:
  # ... build and load the image as ghcr.io/org/repo/runtime:tag ...
  - uses: Mobility-Data-Space/.github/actions/trivy-image-scan@main
    with:
      image-ref: ghcr.io/${{ steps.repo.outputs.lowercase }}/${{ matrix.runtime }}:${{ steps.meta.outputs.version }}
      report-name: trivy-${{ matrix.runtime }}
      output-file: trivy-${{ matrix.runtime }}.txt
```

## Inputs

| Input             | Default                | Description                                                              |
|-------------------|------------------------|--------------------------------------------------------------------------|
| `image-ref`       | _(required)_           | Reference of the image to scan (e.g. `ghcr.io/org/repo/runtime:tag`).    |
| `scanners`        | `vuln`                 | Comma-separated Trivy scanners to run.                                   |
| `report-severity` | `MEDIUM,HIGH,CRITICAL` | Severities included in the (non-failing) report scan.                    |
| `fail-severity`   | `HIGH,CRITICAL`        | Severities that cause the failing scan to exit non-zero.                 |
| `ignore-unfixed`  | `true`                 | Whether the failing scan ignores vulnerabilities with no fix available. |
| `trivyignore`     | `.trivyignore`         | Path to a Trivy ignore file applied to the failing scan.                 |
| `report-name`     | `trivy-image`          | Name of the uploaded report artifact.                                    |
| `output-file`     | `trivy-image.txt`      | File the report scan writes to and uploads.                              |

## Pinned versions

Third-party actions are pinned to commit SHAs. When bumping, update both the SHA
and its trailing `# vX.Y.Z` comment.
