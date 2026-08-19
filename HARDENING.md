<!-- markdownlint-disable -->

# Hardening Report: erzz--dockle-action/v1.3.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **erzz--dockle-action/v1.3.2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (b): In the 'Run Dockle' step of action.yml, the env var $EXIT_CODE (sourced from inputs.exit-code via env: EXIT_CODE: ${{ inputs.exit-code }}) is expanded unquoted in the run block: `dockle --exit-code $EXIT_CODE --exit-level "$FAILURE_THRESHOLD" "$IMAGE"`. An attacker-controlled value with shell metacharacters could cause command injection. Additionally, in the 'Install Dockle' step, ${DOCKLE_VERSION} is used unquoted inside a URL: `curl -L -o dockle.deb https://github.com/.../v${DOCKLE_VERSION}/dockle_${DOCKLE_VERSION}_Linux-64bit.deb`, where DOCKLE_VERSION is sourced from inputs.dockle-version. Both expansions must be double-quoted.

Locations:

- `action.yml:68`
- `action.yml:57`

### unpinned-uses (severity: high)

Multiple workflow files reference actions by mutable tag or branch refs instead of full 40-character SHA digests. In release.yml: `uses: erzz/workflows/.github/workflows/semantic-release.yml@main` (branch ref). In tests.yml: `uses: actions/checkout@v3` (tag), `uses: actions/upload-artifact@v3` (tag, used twice), `uses: github/codeql-action/upload-sarif@v2` (tag). All should be pinned to immutable commit SHAs.

Locations:

- `.github/workflows/release.yml:6`
- `.github/workflows/tests.yml:7`
- `.github/workflows/tests.yml:13`
- `.github/workflows/tests.yml:25`
- `.github/workflows/tests.yml:38`
- `.github/workflows/tests.yml:43`

### missing-permissions (severity: medium)

Neither release.yml nor tests.yml define a top-level `permissions:` key, and no individual jobs within them define job-level `permissions:` blocks. Without explicit permissions, workflows run with the default (potentially broad) token permissions. Each workflow should declare minimal required permissions explicitly.

Locations:

- `.github/workflows/release.yml:1`
- `.github/workflows/tests.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

1. script-injection (action.yml): Quoted `$EXIT_CODE` in the `dockle --exit-code` command (line 68) and quoted the `${DOCKLE_VERSION}` URL in the curl command (line 57) to prevent shell metacharacter injection. 2. unpinned-uses: Pinned all mutable refs to full commit SHAs — `erzz/workflows@main` → `@c0da8d01285ca712064b1877b54e2872eeca3881` in release.yml; `actions/checkout@v3` → `@a37ce9120846195fa4ece8f58b268e6043cb2f26`, `actions/upload-artifact@v3` → `@ff15f0306b3f739f7b6fd43fb5d26cd321bd4de5` (both occurrences), and `github/codeql-action/upload-sarif@v2` → `@b8d3b6e8af63cde30bdc382c0bc28114f4346c88` in tests.yml. 3. missing-permissions: Added `permissions: {}` top-level block to both release.yml and tests.yml.

