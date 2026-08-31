# GitHub Workflows

Reusable, security-pinned GitHub Actions workflows for `vcolombo` public repositories.

## Semgrep Community Edition

`.github/workflows/semgrep.yml` runs an advisory Semgrep CE scan on pull requests. It:

- uses a digest-pinned Semgrep container;
- pins `actions/checkout` to a full commit SHA;
- requires only `contents: read`;
- uses no secrets or paid services;
- checks out Semgrep community rules at an immutable commit SHA;
- keeps Semgrep metrics and version checks disabled;
- prevents repository-controlled ignore files from suppressing tracked findings;
- prevents checkout credentials from persisting into scan steps;
- reports findings without failing the pull request.

Callers must pin this repository to a full commit SHA:

```yaml
jobs:
  semgrep:
    uses: vcolombo/github-workflows/.github/workflows/semgrep.yml@<full-commit-sha>
```

Private repositories can avoid GitHub-hosted runner charges by selecting an existing **repository-scoped Linux x64** self-hosted runner:

```yaml
jobs:
  semgrep:
    uses: vcolombo/github-workflows/.github/workflows/semgrep.yml@<full-commit-sha>
    with:
      use_self_hosted: true
```

Never enable this input from public or fork caller repositories, or on a runner shared with trusted workloads.

## Updating

Update and verify this repository first, then open caller pull requests that replace the old workflow SHA with the new one. Never reference a branch or movable tag.
