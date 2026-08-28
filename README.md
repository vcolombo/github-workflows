# GitHub Workflows

Reusable, security-pinned GitHub Actions workflows for `vcolombo` public repositories.

## Semgrep Community Edition

`.github/workflows/semgrep.yml` runs an advisory Semgrep CE scan on pull requests. It:

- uses a digest-pinned Semgrep container;
- pins `actions/checkout` to a full commit SHA;
- requires only `contents: read`;
- uses no secrets or paid services;
- downloads the `p/default` ruleset with an expected SHA-256, failing closed on upstream drift;
- keeps Semgrep metrics disabled;
- reports findings without failing the pull request.

Callers must pin this repository to a full commit SHA:

```yaml
jobs:
  semgrep:
    uses: vcolombo/github-workflows/.github/workflows/semgrep.yml@<full-commit-sha>
```

## Updating

Update and verify this repository first, then open caller pull requests that replace the old workflow SHA with the new one. Never reference a branch or movable tag.
