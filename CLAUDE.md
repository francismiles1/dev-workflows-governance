# dev-workflows-governance

## Project
A Claude Code plugin providing governance layer — signoff gates, TIDY stage, operation-specific checklists, and anti-rationalization hooks.

## Stack
- Plain markdown files (no build step, no dependencies)
- Claude Code plugin format (.claude-plugin/plugin.json)
- MIT licensed

## Key Commands
- Lint: none (markdown only)
- Tests: none currently
- Install check: verify plugin.json is valid JSON — `python -m json.tool .claude-plugin/plugin.json`

## Structure
```
.claude-plugin/plugin.json   — plugin manifest
agents/                      — wrap-up, signoff, governance-audit
commands/                    — tidy, signoff, audit
skills/checklist-vs-rules/   — core insight skill
hooks/                       — anti-rationalization Stop hook
README.md                    — user-facing documentation
LICENSE                      — MIT
```

## Contribution Target
This plugin is being contributed to shinpr/claude-code-workflows marketplace.
Contribution PR goes to: https://github.com/francismiles1/claude-code-workflows (fork)
Upstream: https://github.com/shinpr/claude-code-workflows

## Before Signoff — Mandatory Checklist
- [ ] plugin.json is valid JSON — run: `python -m json.tool .claude-plugin/plugin.json`
- [ ] All agent frontmatter has name and description fields
- [ ] All command frontmatter has description field
- [ ] SKILL.md frontmatter has name and description fields
- [ ] README.md accurately reflects current file structure
- [ ] No junk files (*.tmp, *.bak, .DS_Store)
- [ ] All files exist in project tree (not private memory) — verify: `git status`
- [ ] Clean commit created

## Workflow
PLAN → implement changes → /tidy → /signoff → push

## Notes
- hooks/anti-rationalization.json is documentation for users, not active in this repo
- The plugin has no runtime dependencies — all files are markdown or JSON
- Versioning follows semver — bump version in .claude-plugin/plugin.json on changes