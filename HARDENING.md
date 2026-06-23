<!-- markdownlint-disable -->

# Hardening Report: erzz--dockle-action/v1.3.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **erzz--dockle-action/v1.3.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation in the 'Install Dockle' step: the env var `${DOCKLE_VERSION}` (sourced from `${{ inputs.dockle-version }}`) is expanded unquoted inside a URL in the `curl` command: `curl -L -o dockle.deb https://github.com/goodwithtech/dockle/releases/download/v${DOCKLE_VERSION}/dockle_${DOCKLE_VERSION}_Linux-64bit.deb`. An attacker-controlled value could inject shell metacharacters.

Locations:

- `action.yml:62`

### script-injection (severity: high)

Rule (b) violation in the 'Run Dockle' step: the env var `$EXIT_CODE` (sourced from `${{ inputs.exit-code }}`) is expanded unquoted in the shell command: `dockle --exit-code $EXIT_CODE --exit-level "$FAILURE_THRESHOLD" "$IMAGE"`. An attacker-controlled value could inject shell metacharacters. It should be quoted as `"$EXIT_CODE"`.

Locations:

- `action.yml:80`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two script-injection findings in action.yml:
1. Line 62 (Install Dockle step): Wrapped the curl URL in double quotes so that `${DOCKLE_VERSION}` expansions are properly quoted: `curl -L -o dockle.deb "https://github.com/goodwithtech/dockle/releases/download/v${DOCKLE_VERSION}/dockle_${DOCKLE_VERSION}_Linux-64bit.deb"`.
2. Line 80 (Run Dockle step): Added double quotes around `$EXIT_CODE` in the dockle command: `dockle --exit-code "$EXIT_CODE" --exit-level "$FAILURE_THRESHOLD" "$IMAGE"`.

