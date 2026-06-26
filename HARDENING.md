<!-- markdownlint-disable -->

# Hardening Report: erzz--dockle-action/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **erzz--dockle-action/v1.4.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In the 'Run Dockle' step, the env var $EXIT_CODE (sourced from inputs.exit-code, a workflow-controllable value) is used unquoted in the run block: `dockle --exit-code $EXIT_CODE --exit-level "$FAILURE_THRESHOLD" "$IMAGE"`. An attacker could inject shell metacharacters via this input. Fix: quote it as "$EXIT_CODE".

Locations:

- `action.yml:73`

### script-injection (severity: high)

Rule (b) violation: In the 'Install Dockle' step, the env var ${DOCKLE_VERSION} (sourced from inputs.dockle-version, a workflow-controllable value) is used unquoted inside a curl URL string: `curl -L "https://github.com/goodwithtech/dockle/releases/download/v${DOCKLE_VERSION}/dockle_${DOCKLE_VERSION}_Linux-64bit.tar.gz" | tar xz -C "$INSTALL_DIR"`. While inside double quotes, the variable expansion is not separately sanitized and an attacker-controlled version string could inject characters. Fix: validate or sanitize DOCKLE_VERSION before interpolating into the URL.

Locations:

- `action.yml:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two script-injection findings in action.yml:
1. Quoted `$EXIT_CODE` as `"$EXIT_CODE"` in the 'Run Dockle' step's dockle command (line 73) to prevent shell metacharacter injection from the exit-code input.
2. Added strict validation of `DOCKLE_VERSION` in the 'Install Dockle' step using a regex (`^[0-9]+\.[0-9]+\.[0-9]+$`) before interpolating it into the curl URL. This ensures only safe X.Y.Z version strings are accepted, blocking any shell metacharacter injection via the dockle-version input. The validation applies to both user-supplied versions and the version resolved from the GitHub API when 'latest' is specified.

