# Upstream Contribution

## When to use
When preparing a PR to the fork (`francismiles1/claude-code-workflows`) or ultimately to the upstream repository (`shinpr/claude-code-workflows`). This includes initial contribution, updates, and any marketplace submission.

## Risk level: HIGH
PRs to the wrong branch or repository are visible publicly. Marketplace submissions with broken plugin.json waste reviewer time and block acceptance. Force-pushing to a shared fork can destroy collaborator work.

## Reversibility
PRs can be closed. Force-pushed branches cannot be recovered without collaborator help. Marketplace submissions may need manual cleanup by maintainers.

---

## Pre-flight

- [ ] All local changes are committed — run: `git status --short` (must be empty)
- [ ] Local branch is up to date with origin — run: `git fetch origin && git log --oneline origin/main..HEAD`
- [ ] Fork remote exists — run: `git remote -v` (should show `origin` pointing to `francismiles1/dev-workflows-governance`)
- [ ] Plugin passes all validation — run through `plugin-manifest-change.md` verification section
- [ ] Version has been bumped appropriately — see `version-release.md`
- [ ] README.md installation instructions are correct for the target marketplace

## Change steps — PR to fork

- [ ] Push current branch to origin — run: `git push origin main` (or feature branch)
- [ ] Create PR on `francismiles1/claude-code-workflows` if contributing to marketplace
- [ ] PR title follows format: `feat: add dev-workflows-governance plugin` (or `fix:`, `docs:`)
- [ ] PR body includes: what the plugin does, installation instructions, link to this repo

## Change steps — PR to upstream

- [ ] Ensure fork is synced with upstream — run: `gh repo sync francismiles1/claude-code-workflows`
- [ ] Create PR from fork to `shinpr/claude-code-workflows`
- [ ] Follow upstream's contribution guidelines (check their README/CONTRIBUTING.md)
- [ ] Verify marketplace.json format matches upstream's expected schema

## Verification

- [ ] PR targets correct branch (usually `main`) — verify in GitHub UI or `gh pr view`
- [ ] No secrets, credentials, or personal paths in any committed file — run: `git diff origin/main...HEAD | grep -iE '(password|secret|token|api.key|\.env)' || echo "CLEAN"`
- [ ] plugin.json is valid — run: `python -m json.tool .claude-plugin/plugin.json`
- [ ] marketplace.json is valid — run: `python -m json.tool .claude-plugin/marketplace.json`
- [ ] All referenced files exist — verify agent and command paths resolve
- [ ] LICENSE file is present and correct (MIT)
- [ ] No OS artefacts (.DS_Store, Thumbs.db) — run: `git ls-files | grep -iE '(\.DS_Store|Thumbs\.db|desktop\.ini)' || echo "CLEAN"`

## Rollback

1. Close the PR on GitHub — run: `gh pr close <number>`
2. If branch was pushed: it remains on remote but causes no harm if PR is closed
3. If force-push damaged the fork: contact collaborators, check GitHub's branch protection recovery
4. Never force-push to upstream — only push to your own fork

## Common failure modes

| Failure | Cause | Prevention |
|---|---|---|
| PR targets wrong repository | Confused fork vs upstream remote | Always verify remote URL before pushing |
| PR contains personal/local paths | Memory files or local config committed | Run `git diff` review before PR creation |
| Marketplace rejects submission | marketplace.json schema mismatch | Check upstream's expected format before submitting |
| Force-push destroys collaborator work | Used `--force` on shared branch | Never force-push to shared branches |
| PR blocked by upstream CI | Plugin structure doesn't match expected format | Review upstream's plugin examples before submitting |
