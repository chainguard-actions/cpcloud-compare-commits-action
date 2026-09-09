<!-- markdownlint-disable -->

# Hardening Report: cpcloud--compare-commits-action/v5.0.27

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--compare-commits-action/v5.0.27** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tags or version strings instead of pinned 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced tag is moved or the repository is compromised.

auto-rebase.yml:
  - uses: tibdex/github-app-token@v1
  - uses: Label305/AutoRebase@v0.1

ci.yml:
  - uses: actions/checkout@v3
  - uses: actions/setup-node@v3 (appears twice)
  - uses: tibdex/github-app-token@v1
  - uses: actions/checkout@v3 (second occurrence)
  - uses: actions/setup-node@v3 (third occurrence)

codeql-analysis.yml:
  - uses: actions/checkout@v3
  - uses: github/codeql-action/init@v2
  - uses: github/codeql-action/autobuild@v2
  - uses: github/codeql-action/analyze@v2

update-deps.yml:
  - uses: actions/checkout@v3
  - uses: cachix/install-nix-action@v18 (appears twice)
  - uses: tibdex/github-app-token@v1
  - uses: cpcloud/flake-update-action@v1.0.2

Locations:

- `.github/workflows/auto-rebase.yml:16`
- `.github/workflows/auto-rebase.yml:21`
- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:22`
- `.github/workflows/ci.yml:46`
- `.github/workflows/ci.yml:55`
- `.github/workflows/ci.yml:59`
- `.github/workflows/ci.yml:63`
- `.github/workflows/codeql-analysis.yml:25`
- `.github/workflows/codeql-analysis.yml:27`
- `.github/workflows/codeql-analysis.yml:31`
- `.github/workflows/codeql-analysis.yml:33`
- `.github/workflows/update-deps.yml:18`
- `.github/workflows/update-deps.yml:19`
- `.github/workflows/update-deps.yml:33`
- `.github/workflows/update-deps.yml:34`
- `.github/workflows/update-deps.yml:39`
- `.github/workflows/update-deps.yml:43`

### missing-permissions (severity: medium)

Three workflow files have no top-level `permissions:` key and no job-level `permissions:` block on any of their jobs. Without explicit permissions, GitHub Actions defaults to the repository's default token permissions (often broad write access), violating the principle of least privilege.

- auto-rebase.yml: no permissions defined at any level
- ci.yml: no permissions defined at any level (build, test, and release jobs all lack permissions)
- update-deps.yml: no permissions defined at any level (get-flakes and flake-update jobs both lack permissions)

Locations:

- `.github/workflows/auto-rebase.yml:1`
- `.github/workflows/ci.yml:1`
- `.github/workflows/update-deps.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all 18 unpinned action references across 4 workflow files by pinning each to its full 40-character commit SHA (with the original tag preserved as a comment). Added top-level `permissions: contents: read` to auto-rebase.yml, ci.yml, and update-deps.yml (which had no permissions at any level), plus job-level permissions scoped to the minimum required: read-only for build/test/get-flakes jobs, write for release/autorebase/flake-update jobs that create commits or PRs. codeql-analysis.yml already had job-level permissions and only needed a top-level default added.

