---
description: Security scan, README, CI/CD, GitHub Pages, About section, then push to GitHub
argument-hint: [optional commit message]
allowed-tools: Read, Write, Edit, Glob, Grep, PowerShell
---

Publish this project to GitHub (`origin`, repo `varunkhare3000/ClaudeTraining`). Do the steps in order, stop on any failure, and report each outcome faithfully.

Setup (Windows/PowerShell): refresh PATH first with
`$env:Path = [Environment]::GetEnvironmentVariable('Path','Machine') + ';' + [Environment]::GetEnvironmentVariable('Path','User')`.
Pushes use SSH with `~/.ssh/id_ed25519_github` (already set in `core.sshCommand`). Never force-push. Pages URL: `https://varunkhare3000.github.io/ClaudeTraining/`.

## 1. Security scan (gate: do this before anything is committed or pushed)
Scan `index.html`, `CLAUDE.md`, `.claude/` and anything else that will be staged. Fail the run and show the findings if any check fails:
- **Outbound requests:** the only network destination allowed is `formsubmit.co` (the `FORMSUBMIT_ENDPOINT` fetch). Grep for other `http(s)://` hosts, `<script src`, `<link href` to remote hosts, `@import`, `fetch(`, `XMLHttpRequest`, `WebSocket`, `sendBeacon`, `navigator.sendBeacon`, `<img src="http`. Hosts used only inside comments or as the project's own repo link are fine.
- **No storage:** no `localStorage`, `sessionStorage`, `indexedDB`, `document.cookie`.
- **Real email:** `FORMSUBMIT_ENDPOINT` must still contain the placeholder `YOUR_EMAIL@example.com` in anything committed to a public repo. If a real address is present, stop and tell the user (it would be published); do not commit it.
- **Secrets:** grep for private keys (`BEGIN .*PRIVATE KEY`), `ghp_`, `github_pat_`, `AKIA`, `api[_-]?key`, `secret`, `token`, `password`, and any email addresses other than `example.com`.
- **Never staged:** `.ssh/`, `id_*`, `*.pem`, `.env*`, `PDFs/`, `Prompts.txt`. Make sure `.gitignore` lists `PDFs/`, `Prompts.txt`, `.env*`, `*.pem`, `id_*` (create/append as needed).
- Report as a short pass/fail table.

## 2. README.md
Read `index.html` and `CLAUDE.md`. Create or edit `README.md` (keep hand-written content): description (fictitious bank internal IT PMO Kanban demo), features, how to run (open `index.html`, no server), live demo link (the Pages URL), FormSubmit setup (change `FORMSUBMIT_ENDPOINT`; one-time activation email), the in-memory/resets-on-refresh note, a CI badge for the workflow below. Neutral branding only, no real email.

## 3. CI/CD (GitHub Actions + Pages)
Create or update `.github/workflows/ci.yml`. It must:
- trigger on `push` and `pull_request` to `main`, plus `workflow_dispatch`
- job `checks` (ubuntu-latest, `permissions: contents: read`): checkout (`actions/checkout@v4`), then shell steps that fail the build if `index.html` is missing, contains external `<script src=`/`<link href=http`, uses `localStorage|sessionStorage|indexedDB|document.cookie`, contains `!important`, or contains any `fetch(` host other than `formsubmit.co`; also run a secret grep (private keys, `ghp_`, `AKIA`)
- job `deploy` (only on push to `main`, `needs: checks`, `permissions: pages: write, id-token: write, contents: read`, `environment: github-pages`): copy `index.html` into `_site/`, `actions/configure-pages@v5`, `actions/upload-pages-artifact@v3` with `path: _site`, `actions/deploy-pages@v4`
Pin actions to the major versions above. Keep it vanilla: no npm, no build.

## 4. Commit and push
- Stage only: `index.html`, `CLAUDE.md`, `README.md`, `.gitignore`, `.github/`, `.claude/commands/`.
- Commit with `$ARGUMENTS` if given, else a short descriptive message; skip if nothing changed.
- `git pull --rebase origin main`, then `git push -u origin main`. On conflict or rejection, stop and explain.

## 5. GitHub Pages + About section
If `gh` is installed and authenticated (`gh auth status`):
- enable Pages from Actions: `gh api -X POST repos/varunkhare3000/ClaudeTraining/pages -f build_type=workflow` (if it already exists, use `-X PUT`)
- set About: `gh repo edit varunkhare3000/ClaudeTraining --description "Internal IT PMO Kanban board demo (vanilla HTML/CSS/JS, single file, no build)" --homepage "https://varunkhare3000.github.io/ClaudeTraining/" --add-topic kanban,project-management,vanilla-javascript,html,css,demo,github-pages`
- check the latest run: `gh run list --limit 1`

If `gh` is not available, do not look for workarounds or ask for tokens. Give the user these manual steps instead:
1. Repo, Settings, Pages, Source: **GitHub Actions**.
2. Repo page, gear icon next to About: paste the description, tick **Use your GitHub Pages website** (or paste the Pages URL), add the topics, save.

## 6. Report
Show: scan results table, commit hash, repo URL, Pages URL, CI run status, and which About/Pages steps were automatic versus manual.
