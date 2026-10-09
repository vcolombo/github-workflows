# GitHub Workflows

Reusable, security-pinned GitHub Actions workflows for `vcolombo` public repositories.

## Semgrep Community Edition

`.github/workflows/semgrep.yml` runs an advisory Semgrep CE scan on pull requests. It:

- uses a digest-pinned Semgrep container;
- pins `actions/checkout` to a full commit SHA;
- declares no permissions of its own: it inherits the caller's token, so callers that leave `upload_sarif` enabled grant `contents: read` plus `security-events: write`, while callers that set `upload_sarif: false` grant only `contents: read`;
- uploads SARIF findings to the caller's code-scanning alerts when `upload_sarif` is left enabled (code scanning must be enabled on the caller: free for public repos, paid Advanced Security for private ones); with `upload_sarif: false` findings are only summarized in the log;
- runs the scanner in a non-root, network-disabled container with read-only mounts, so self-hosted workspaces stay runner-owned;
- fetches Semgrep community rules at an immutable commit into runner-temporary storage, never the persistent repository workspace;
- keeps Semgrep metrics and version checks disabled;
- prevents repository-controlled ignore files from suppressing tracked findings;
- prevents checkout credentials from persisting into scan steps;
- reports findings without failing the pull request.

Callers must pin this repository to a full commit SHA:

```yaml
permissions:
  contents: read
  security-events: write # required for the SARIF upload; omit if upload_sarif is false

jobs:
  semgrep:
    uses: vcolombo/github-workflows/.github/workflows/semgrep.yml@<full-commit-sha>
```

Private repositories can avoid GitHub-hosted runner charges by selecting an existing **repository-scoped Linux x64** self-hosted runner with Docker installed, running, and accessible to the runner account:
```yaml
jobs:
  semgrep:
    uses: vcolombo/github-workflows/.github/workflows/semgrep.yml@<full-commit-sha>
    with:
      use_self_hosted: true
      upload_sarif: false # private repos without Advanced Security have no code scanning to upload to
```

Self-hosted mode skips fork pull requests. Use it only where every collaborator with write access is trusted, and never on a runner shared with unrelated trusted workloads.

The Semgrep rules pin lives in `.github/semgrep-rules-commit.txt`, outside `.github/workflows/` so the monthly `bump-semgrep-rules` run can push it with the default `GITHUB_TOKEN` (which cannot write workflow files). The scan reads that file from this repository at the exact revision the caller pinned, via the `job` context's workflow identity.

## Updating

Update and verify this repository first, then open caller pull requests that replace the old workflow SHA with the new one. Never reference a branch or movable tag.
