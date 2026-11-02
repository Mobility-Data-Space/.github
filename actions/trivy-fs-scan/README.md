# `trivy-fs-scan`

Shared composite action that runs a [Trivy](https://trivy.dev) **filesystem**
scan based on Gradle dependency output. It:

1. Sets up the JDK (via [`setup-java`](../setup-java)).
2. Generates Gradle dependency lockfiles (`resolveAndLockAll --write-locks`) so
   Trivy can see the resolved dependency tree.
3. Runs a report scan (`MEDIUM,HIGH,CRITICAL`) and uploads it as an artifact.
4. Runs a second scan that fails the build on `HIGH,CRITICAL` findings.

## Usage

```yaml
jobs:
  filesystem-scan:
    name: Trivy Filesystem & Secrets Scan
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: read
    steps:
      - uses: actions/checkout@v5
      - uses: Mobility-Data-Space/.github/actions/trivy-fs-scan@main
```

That single step replaces the `setup-jdk` + lockfile + Trivy report/fail steps
previously duplicated in each repo.

## Inputs

| Input                 | Default                                    | Description                                                              |
|-----------------------|--------------------------------------------|--------------------------------------------------------------------------|
| `java-version`        | `25`                                       | Java version used to generate the Gradle lockfiles.                      |
| `gradle-lock-command` | `./gradlew resolveAndLockAll --write-locks`| Gradle command that resolves and writes the dependency lockfiles.        |
| `username`            | `${{ github.actor }}`                      | Username Gradle uses to authenticate against GitHub Packages.            |
| `token`               | `${{ github.token }}`                      | Token Gradle uses to authenticate against GitHub Packages.               |
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
