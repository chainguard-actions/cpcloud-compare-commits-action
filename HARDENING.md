<!-- markdownlint-disable -->

# Hardening Report: cpcloud--compare-commits-action/v5.0.37

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--compare-commits-action/v5.0.37** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tags or version strings instead of immutable 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced tag is moved or the repository is compromised.

auto-rebase.yml: `tibdex/github-app-token@v2`, `Label305/AutoRebase@v0.1`
ci.yml: `actions/checkout@v4`, `actions/setup-node@v3` (×2), `tibdex/github-app-token@v2`
codeql-analysis.yml: `actions/checkout@v4`, `github/codeql-action/init@v2`, `github/codeql-action/autobuild@v2`, `github/codeql-action/analyze@v2`
update-deps.yml: `actions/checkout@v4` (×2), `tibdex/github-app-token@v2`, `cpcloud/flake-update-action@v1.0.4`

Locations:

- `.github/workflows/auto-rebase.yml:16`
- `.github/workflows/auto-rebase.yml:21`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:22`
- `.github/workflows/ci.yml:55`
- `.github/workflows/ci.yml:61`
- `.github/workflows/ci.yml:65`
- `.github/workflows/codeql-analysis.yml:29`
- `.github/workflows/codeql-analysis.yml:31`
- `.github/workflows/codeql-analysis.yml:34`
- `.github/workflows/codeql-analysis.yml:36`
- `.github/workflows/update-deps.yml:16`
- `.github/workflows/update-deps.yml:33`
- `.github/workflows/update-deps.yml:40`
- `.github/workflows/update-deps.yml:44`

### missing-permissions (severity: medium)

Three workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, workflows run with the default (potentially broad) token permissions, violating the principle of least privilege.

- auto-rebase.yml: single job `autorebase` has no permissions block
- ci.yml: jobs `build`, `test`, and `release` all have no permissions block
- update-deps.yml: jobs `get-flakes` and `flake-update` have no permissions block

Locations:

- `.github/workflows/auto-rebase.yml:1`
- `.github/workflows/ci.yml:1`
- `.github/workflows/update-deps.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all 15 unpinned action references across 4 workflow files by pinning each to its full 40-character commit SHA (with original tag preserved as a comment). Added top-level `permissions: {}` to auto-rebase.yml, ci.yml, update-deps.yml, and codeql-analysis.yml. Added minimal job-level permissions to each job: autorebase (contents:write, pull-requests:write), build/test (contents:read), release (contents:write), get-flakes (contents:read), flake-update (contents:write, pull-requests:write). The codeql-analysis.yml already had correct job-level permissions and only needed the top-level block and SHA pinning.

