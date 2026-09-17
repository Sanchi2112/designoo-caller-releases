## Secrets — hard rules (never break)
1. Never print, echo, log or paste a secret value in terminal output, reports, commits, PRs or docs. Refer to secrets by NAME + location + first 4 chars + length only.
2. Never commit secrets: no .env*, *.pem, *.key, service-account JSON, creds.json, auth_info/, tokens. If one is found tracked, STOP and report — do not "fix" by rewriting history without approval.
3. Client apps (Electron, Expo/mobile, Android, browser bundles) are PUBLIC. They may only ship: Supabase URL, anon/publishable key, public API base URLs, Firebase client config. Never S3/R2 keys, service_role, sb_secret_, GitHub PATs, CRON_SECRET, Bitrix webhooks, Google client secrets, pdf-server/WA secrets, Telegram tokens, MCP URLs.
4. Packaged client env files are GENERATED from an allowlist script — never copy the developer's .env into a build (no `extraResources: .env`).
5. Every client build runs a guard that fails if the packaged env/bundle contains a non-allowlisted name or a secret pattern (AKIA, github_pat_, ghp_, sb_secret_, service_role JWT, GOCSPX, PRIVATE KEY, Bitrix /rest/<id>/<token>/).
6. Server-only values live in Coolify env (or the box's root-only .env files, mode 600) — never in a client, never in git.
7. Uploads/downloads from clients use server-signed URLs or authenticated server routes — clients never hold storage credentials.
8. Secrets never go in URLs/query strings (they end up in access logs) — use headers.
9. If a secret is exposed anywhere (repo, installer, log, chat, transcript): report immediately with rotation order; rotation beats cleanup.
10. When a user pastes a secret into this session: use it only to set it where asked, via env/stdin, then tell the user which transcript file holds it.

### Secret scan hook (gitleaks)
- `.githooks/pre-commit` scans staged changes with gitleaks using `.gitleaks.toml` (gitleaks defaults + the rule 5 patterns).
- Enable once per clone: `git config core.hooksPath .githooks`. If the clone already has local hooks in `.git/hooks` (e.g. graphify's), leave `core.hooksPath` unset and add `.git/hooks/pre-commit` containing `exec sh "$(git rev-parse --show-toplevel)/.githooks/pre-commit"` instead.
- Install gitleaks: Windows `winget install --id Gitleaks.Gitleaks` or `scoop install gitleaks`; macOS `brew install gitleaks`; Linux: binary from https://github.com/gitleaks/gitleaks/releases. Until it is installed the hook prints a loud warning and lets the commit through.
- Full-tree scan: `gitleaks dir . --config .gitleaks.toml --redact` (gitleaks < 8.19: `gitleaks detect --no-git --source . --config .gitleaks.toml --redact`).
