APK releases for Designoo Caller self-update. Source lives in the private designoo-caller repo.

## Secret scan hook

This repo has a gitleaks pre-commit hook (`.githooks/pre-commit`, config `.gitleaks.toml`). Enable it once per clone:

```bash
git config core.hooksPath .githooks
```

Install gitleaks first (`winget install --id Gitleaks.Gitleaks`, `scoop install gitleaks` or `brew install gitleaks`); without it the hook warns and skips. The rules it enforces are in CLAUDE.md under "Secrets — hard rules".
