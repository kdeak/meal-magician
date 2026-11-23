# pr-4: gh-3 Install version 0.76.2 of Trivy

## Description

Closes #3
### What changed
Pinned `version: v0.76.2` on both `trivy-action` steps in `.github/workflows/ci.yml`, upgrading from the implicit default of 0.70.0.

### Key decisions
- Kept the existing pinned SHA for `aquasecurity/trivy-action@...# v0.36.0` (that's the action version, not the Trivy binary version) and added the Trivy `version` input, which is the documented way to control the installed Trivy release via `setup-trivy`.
- Applied to both the gate scan and the SARIF scan steps so both stay in sync.

### How to verify
```bash
grep -A1 "exit-code" .github/workflows/ci.yml
```
CI will exercise the real scan on next run.
