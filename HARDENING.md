<!-- markdownlint-disable -->

# Hardening Report: cpcloud--compare-commits-action/v5.0.10

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--compare-commits-action/v5.0.10** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files use mutable tag/version refs instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks if the referenced action is compromised or a tag is moved. Failing references include: auto-rebase.yml — `tibdex/github-app-token@v1`, `Label305/AutoRebase@v0.1`; ci.yml — `actions/checkout@v2`, `actions/setup-node@v2`, `tibdex/github-app-token@v1`; codeql-analysis.yml — `actions/checkout@v2`, `github/codeql-action/init@v1`, `github/codeql-action/autobuild@v1`, `github/codeql-action/analyze@v1`; update-deps.yml — `actions/checkout@v2`, `cachix/install-nix-action@v16`, `cpcloud/flake-dep-info-action@main`, `cpcloud/compare-commits-action@v5.0.9`, `tibdex/github-app-token@v1`, `peter-evans/create-pull-request@v3`, `peter-evans/enable-pull-request-automerge@v1`.

Locations:

- `.github/workflows/auto-rebase.yml:15`
- `.github/workflows/auto-rebase.yml:21`
- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:21`
- `.github/workflows/ci.yml:44`
- `.github/workflows/ci.yml:56`
- `.github/workflows/ci.yml:65`
- `.github/workflows/ci.yml:70`
- `.github/workflows/codeql-analysis.yml:28`
- `.github/workflows/codeql-analysis.yml:33`
- `.github/workflows/codeql-analysis.yml:38`
- `.github/workflows/codeql-analysis.yml:41`
- `.github/workflows/update-deps.yml:13`
- `.github/workflows/update-deps.yml:18`
- `.github/workflows/update-deps.yml:26`
- `.github/workflows/update-deps.yml:33`
- `.github/workflows/update-deps.yml:39`
- `.github/workflows/update-deps.yml:52`
- `.github/workflows/update-deps.yml:62`
- `.github/workflows/update-deps.yml:72`

### script-injection (severity: high)

Sub-rule (a): A `${{ ... }}` expression is interpolated directly inside a `run:` shell command string. In update-deps.yml, the step 'Update ${{ matrix.input }}' runs: `nix flake lock --update-input ${{ matrix.input }}`. The `matrix.input` value is substituted directly into the shell command before the shell parses it, allowing an attacker who can influence the matrix (e.g. via a forked workflow or repository configuration) to inject arbitrary shell commands. The value should be passed via an `env:` variable and double-quoted in the shell script instead.

Locations:

- `.github/workflows/update-deps.yml:29`

### missing-permissions (severity: medium)

Three workflow files have no top-level `permissions:` block and no job-level `permissions:` blocks on any of their jobs. Without explicit permissions, workflows inherit the default repository permissions (which may be `write-all` for older repositories), granting unnecessary access to the GITHUB_TOKEN. Each workflow should declare minimal required permissions at the top level or per job. Affected files: auto-rebase.yml, ci.yml, update-deps.yml.

Locations:

- `.github/workflows/auto-rebase.yml:1`
- `.github/workflows/ci.yml:1`
- `.github/workflows/update-deps.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all 20 unpinned action references across auto-rebase.yml, ci.yml, codeql-analysis.yml, and update-deps.yml by pinning each to its full 40-character SHA with the original tag as a comment. Added `permissions: {}` top-level blocks to auto-rebase.yml, ci.yml, and update-deps.yml. Fixed script injection in update-deps.yml by moving `${{ matrix.input }}` from the `run:` shell command into an `env:` block as `MATRIX_INPUT`, then referencing it as `"$MATRIX_INPUT"` in the shell script.

