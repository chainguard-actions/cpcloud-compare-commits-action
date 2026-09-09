<!-- markdownlint-disable -->

# Hardening Report: cpcloud--compare-commits-action/v5.0.28

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--compare-commits-action/v5.0.28** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tags/versions instead of pinned full 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks if the referenced tag is moved or the repository is compromised.

Failing references:
- auto-rebase.yml: `tibdex/github-app-token@v1`, `Label305/AutoRebase@v0.1`
- ci.yml: `actions/checkout@v3`, `actions/setup-node@v3`, `tibdex/github-app-token@v1`, `actions/checkout@v3`, `actions/setup-node@v3`, `actions/checkout@v3`
- codeql-analysis.yml: `actions/checkout@v3`, `github/codeql-action/init@v2`, `github/codeql-action/autobuild@v2`, `github/codeql-action/analyze@v2`
- update-deps.yml: `actions/checkout@v3`, `cachix/install-nix-action@v18`, `actions/checkout@v3`, `cachix/install-nix-action@v18`, `tibdex/github-app-token@v1`, `cpcloud/flake-update-action@v1.0.4`

Locations:

- `.github/workflows/auto-rebase.yml:14`
- `.github/workflows/auto-rebase.yml:20`
- `.github/workflows/ci.yml:18`
- `.github/workflows/ci.yml:21`
- `.github/workflows/ci.yml:44`
- `.github/workflows/ci.yml:53`
- `.github/workflows/ci.yml:60`
- `.github/workflows/ci.yml:63`
- `.github/workflows/codeql-analysis.yml:27`
- `.github/workflows/codeql-analysis.yml:29`
- `.github/workflows/codeql-analysis.yml:31`
- `.github/workflows/codeql-analysis.yml:33`
- `.github/workflows/update-deps.yml:18`
- `.github/workflows/update-deps.yml:19`
- `.github/workflows/update-deps.yml:33`
- `.github/workflows/update-deps.yml:34`
- `.github/workflows/update-deps.yml:37`
- `.github/workflows/update-deps.yml:41`

### missing-permissions (severity: medium)

Three workflow files have no top-level `permissions:` key and no job-level `permissions:` block on any of their jobs. Without explicit permissions, workflows run with the default (often broad) token permissions, violating the principle of least privilege.

- auto-rebase.yml: No permissions defined at top level or on the `autorebase` job.
- ci.yml: No permissions defined at top level or on the `build`, `test`, or `release` jobs.
- update-deps.yml: No permissions defined at top level or on the `get-flakes` or `flake-update` jobs.

Locations:

- `.github/workflows/auto-rebase.yml:1`
- `.github/workflows/ci.yml:1`
- `.github/workflows/update-deps.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all 18 unpinned action references across 4 workflow files by resolving each tag to its full 40-character SHA commit hash (preserving the original tag as a comment). Added `permissions: {}` top-level blocks to auto-rebase.yml, ci.yml, and update-deps.yml which lacked any permissions declarations. codeql-analysis.yml already had job-level permissions and was not listed in the missing-permissions finding, so only its unpinned actions were pinned.

