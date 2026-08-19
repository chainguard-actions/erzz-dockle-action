<!-- markdownlint-disable -->

# Hardening Report: erzz--dockle-action/v1.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **erzz--dockle-action/v1.3.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Workflow files reference external actions by mutable tag or branch refs instead of full 40-character commit SHAs. In release.yml: `erzz/workflows/.github/workflows/semantic-release.yml@main` (branch ref). In tests.yml: `actions/checkout@v3`, `actions/upload-artifact@v3`, and `github/codeql-action/upload-sarif@v2` (all version tags, not SHAs). These can be silently updated by the upstream maintainer, enabling supply-chain attacks.

Locations:

- `.github/workflows/release.yml:6`
- `.github/workflows/tests.yml:8`
- `.github/workflows/tests.yml:12`
- `.github/workflows/tests.yml:33`
- `.github/workflows/tests.yml:44`

### missing-permissions (severity: medium)

Neither workflow file defines a top-level `permissions:` block, and no job within them defines job-level permissions. Without explicit permissions, workflows run with the default (potentially broad) token permissions. Both release.yml and tests.yml are affected.

Locations:

- `.github/workflows/release.yml:1`
- `.github/workflows/tests.yml:1`

### script-injection (severity: high)

Sub-rule (b): Unquoted shell variable expansions of workflow-controllable inputs in action.yml run blocks. (1) In the 'Install Dockle' step (line ~60), `${DOCKLE_VERSION}` (sourced from `inputs.dockle-version`) is used unquoted inside a URL: `curl -L -o dockle.deb https://github.com/goodwithtech/dockle/releases/download/v${DOCKLE_VERSION}/dockle_${DOCKLE_VERSION}_Linux-64bit.deb`. An attacker-controlled value containing shell metacharacters could break out of the URL context. (2) In the 'Run Dockle' step (line ~76), `$EXIT_CODE` (sourced from `inputs.exit-code`) is used unquoted: `dockle --exit-code $EXIT_CODE --exit-level "$FAILURE_THRESHOLD" "$IMAGE"`. Both should be double-quoted: `"${DOCKLE_VERSION}"` and `"$EXIT_CODE"`.

Locations:

- `action.yml:60`
- `action.yml:76`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings: (1) Pinned all mutable action/workflow refs to full 40-char SHAs with tag comments: actions/checkout@v3→a37ce91, actions/upload-artifact@v3→ff15f03 (both jobs), github/codeql-action/upload-sarif@v2→b8d3b6e, erzz/workflows@main→c0da8d0. (2) Added `permissions: {}` top-level blocks to both release.yml and tests.yml. (3) Fixed script injection in action.yml: wrapped the curl URL in double quotes to properly quote ${DOCKLE_VERSION}, and added double quotes around $EXIT_CODE in the dockle run command.

