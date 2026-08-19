<!-- markdownlint-disable -->

# Hardening Report: erzz--dockle-action/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **erzz--dockle-action/v1.4.1** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): ${{ }} expressions are interpolated directly inside run: shell command strings in the 'Get Release SHA' step. Line 53: `SHA=$(git show-ref --hash v${{ needs.release.outputs.new_release_version }})` and line 54: `echo "SHA for v${{ needs.release.outputs.new_release_version }}: $SHA"`. The value of needs.release.outputs.new_release_version flows through YAML template substitution before the shell sees it, enabling script injection. Additionally, line 21 interpolates `${{ secrets.GITHUB_TOKEN }}` directly inside a curl command in a run: block.

Locations:

- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:53`
- `.github/workflows/release.yml:54`

### script-injection (severity: high)

Rule (b): In action.yml, the env var $EXIT_CODE (sourced from inputs.exit-code, a workflow-controllable value) is used unquoted in the run: block: `dockle --exit-code $EXIT_CODE --exit-level "$FAILURE_THRESHOLD" "$IMAGE"`. An unquoted shell expansion allows shell metacharacters in the input value to be interpreted by the shell.

Locations:

- `action.yml:83`

### missing-permissions (severity: medium)

The workflow file release.yml has no top-level `permissions:` key and neither of its jobs (release, floating-tag) defines a job-level `permissions:` block. This means the workflow runs with the default (overly broad) GitHub Actions token permissions.

Locations:

- `.github/workflows/release.yml:1`

### missing-permissions (severity: medium)

The workflow file tests.yml has no top-level `permissions:` key and neither of its jobs (basic-pass, advanced) defines a job-level `permissions:` block. This means the workflow runs with the default (overly broad) GitHub Actions token permissions.

Locations:

- `.github/workflows/tests.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed all 4 findings:
1. release.yml line 21: Moved `${{ secrets.GITHUB_TOKEN }}` into step env block as `GITHUB_TOKEN`, referenced as `${GITHUB_TOKEN}` in curl command.
2. release.yml lines 53-54: Moved `${{ needs.release.outputs.new_release_version }}` into step env block as `NEW_RELEASE_VERSION`, referenced as `${NEW_RELEASE_VERSION}` in shell. Also moved `${{ needs.release.outputs.new_release_major_version }}` to env block in the github-script step, referenced via `process.env.NEW_RELEASE_MAJOR_VERSION`.
3. action.yml line 83: Quoted `$EXIT_CODE` → `"$EXIT_CODE"` to prevent shell metacharacter injection.
4. release.yml: Added top-level `permissions: contents: write` and per-job `permissions: contents: write` for both jobs (needed for tag/release creation).
5. tests.yml: Added top-level `permissions: {}` since no special permissions are needed.

