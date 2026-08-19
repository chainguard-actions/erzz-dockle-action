<!-- markdownlint-disable -->

# Hardening Report: erzz--dockle-action/v1.3.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **erzz--dockle-action/v1.3.1** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (b): Unquoted shell variable expansion of untrusted data. In the 'Run Dockle' step, `$EXIT_CODE` is expanded without double-quotes: `dockle --exit-code $EXIT_CODE ...`. The variable `EXIT_CODE` is sourced from `inputs.exit-code` (a workflow-controllable value via the `env:` block), so an attacker-controlled value containing shell metacharacters (`;`, `|`, `&`, etc.) could inject arbitrary commands. Fix: use `"$EXIT_CODE"` instead.

Locations:

- `action.yml:80`

### script-injection (severity: high)

Sub-rule (b): Unquoted shell variable expansion of untrusted data. In the 'Install Dockle' step, `${DOCKLE_VERSION}` is used unquoted inside a curl URL: `curl -L -o dockle.deb https://github.com/.../v${DOCKLE_VERSION}/dockle_${DOCKLE_VERSION}_Linux-64bit.deb`. The variable `DOCKLE_VERSION` is sourced from `inputs.dockle-version` (workflow-controllable). An attacker-supplied value with shell metacharacters could inject arbitrary commands. Fix: quote the entire URL string, e.g. `"https://.../${DOCKLE_VERSION}/..."`.

Locations:

- `action.yml:63`

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable tags or branch names instead of full 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the referenced tag or branch is moved or overwritten. Failing references: `erzz/workflows/.github/workflows/semantic-release.yml@main` (branch ref) in release.yml; `actions/checkout@v3`, `actions/upload-artifact@v3`, and `github/codeql-action/upload-sarif@v2` (tag refs) in tests.yml.

Locations:

- `.github/workflows/release.yml:6`
- `.github/workflows/tests.yml:8`
- `.github/workflows/tests.yml:14`
- `.github/workflows/tests.yml:27`
- `.github/workflows/tests.yml:38`
- `.github/workflows/tests.yml:44`

### missing-permissions (severity: medium)

Neither `release.yml` nor `tests.yml` declares a top-level `permissions:` key, and no job in either file has a job-level `permissions:` block. Without explicit permissions, workflows run with the default (potentially broad) token permissions. Each workflow should declare `permissions: {}` at the top level and grant only the specific scopes required.

Locations:

- `.github/workflows/release.yml:1`
- `.github/workflows/tests.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed 4 findings across 3 files:
1. action.yml line 63: Quoted the curl URL string containing ${DOCKLE_VERSION} to prevent shell metacharacter injection.
2. action.yml line 80: Quoted $EXIT_CODE → "$EXIT_CODE" in the dockle run command to prevent shell metacharacter injection.
3. .github/workflows/release.yml: Pinned erzz/workflows@main to full SHA c0da8d01285ca712064b1877b54e2872eeca3881 and added top-level `permissions: {}`.
4. .github/workflows/tests.yml: Pinned actions/checkout@v3 to a37ce9120846195fa4ece8f58b268e6043cb2f26, actions/upload-artifact@v3 to ff15f0306b3f739f7b6fd43fb5d26cd321bd4de5, and github/codeql-action/upload-sarif@v2 to b8d3b6e8af63cde30bdc382c0bc28114f4346c88; added top-level `permissions: {}`.

