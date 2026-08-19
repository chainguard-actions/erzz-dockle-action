<!-- markdownlint-disable -->

# Hardening Report: erzz--dockle-action/v1.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **erzz--dockle-action/v1.4.0** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In the 'Run Dockle' step of action.yml, the env var EXIT_CODE is sourced from inputs.exit-code (a workflow-controllable input) and is expanded unquoted in the run: block: `dockle --exit-code $EXIT_CODE --exit-level "$FAILURE_THRESHOLD" "$IMAGE"`. An unquoted shell variable expansion of untrusted data allows shell metacharacter injection (e.g. semicolons, pipes, command substitution). It should be quoted as `"$EXIT_CODE"`.

Locations:

- `action.yml:68`

### unpinned-uses (severity: high)

Multiple uses: references are pinned to mutable tags or branch names instead of full 40-character commit SHAs:
- release.yml: `uses: erzz/workflows/.github/workflows/semantic-release.yml@main` (branch ref 'main')
- tests.yml: `uses: actions/checkout@v3` (tag ref 'v3')
- tests.yml: `uses: actions/upload-artifact@v3` (tag ref 'v3', appears twice)

Locations:

- `.github/workflows/release.yml:7`
- `.github/workflows/tests.yml:10`
- `.github/workflows/tests.yml:16`
- `.github/workflows/tests.yml:33`
- `.github/workflows/tests.yml:43`

### missing-permissions (severity: medium)

Neither release.yml nor tests.yml defines a top-level `permissions:` block, and no individual job within either file defines a `permissions:` block. Without explicit permissions, workflows run with the default (potentially broad) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/release.yml:1`
- `.github/workflows/tests.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed three findings: (1) script-injection in action.yml: quoted `$EXIT_CODE` as `"$EXIT_CODE"` in the dockle run command to prevent shell metacharacter injection; (2) unpinned-uses: pinned actions/checkout@v3 to SHA a37ce9120846195fa4ece8f58b268e6043cb2f26, actions/upload-artifact@v3 to SHA ff15f0306b3f739f7b6fd43fb5d26cd321bd4de5, and erzz/workflows@main to SHA c0da8d01285ca712064b1877b54e2872eeca3881 in tests.yml and release.yml; (3) missing-permissions: added `permissions: {}` top-level blocks to both .github/workflows/release.yml and .github/workflows/tests.yml.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed the unquoted curl URL in the 'Install Dockle' step of action.yml (line 62). The URL `https://github.com/goodwithtech/dockle/releases/download/v${DOCKLE_VERSION}/dockle_${DOCKLE_VERSION}_Linux-64bit.deb` was wrapped in double quotes to prevent shell metacharacters in the `DOCKLE_VERSION` environment variable (sourced from the `dockle-version` input) from being interpreted as shell commands.

