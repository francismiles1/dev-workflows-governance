# Plugin Manifest Change

## When to use
Any modification to `.claude-plugin/plugin.json` — adding/removing commands, agents, changing metadata, updating author info, or modifying keywords.

## Risk level: HIGH
A malformed or structurally invalid plugin.json silently breaks installation for every user. No error is shown at install time for some structural issues — the plugin simply fails to load agents or commands.

## Reversibility
`git checkout -- .claude-plugin/plugin.json` restores the previous version. If already pushed and users have installed, they must re-install.

---

## Pre-flight

- [ ] Read current `.claude-plugin/plugin.json` before making changes
- [ ] Identify what is changing and why — state it explicitly
- [ ] Check if `marketplace.json` also needs updating (description, version, metadata must stay in sync)

## Change steps

- [ ] Edit `.claude-plugin/plugin.json`
- [ ] If adding a new command path: verify the file exists — `ls <path>`
- [ ] If adding a new agent path: verify the file exists — `ls <path>`
- [ ] If changing version: follow semver (see `version-release.md`)
- [ ] If changing description: update `marketplace.json` description to match

## Verification

- [ ] Validate JSON syntax — run: `python -m json.tool .claude-plugin/plugin.json`
- [ ] Validate marketplace JSON — run: `python -m json.tool .claude-plugin/marketplace.json`
- [ ] Every path in `commands` array resolves to an existing file — run: `for f in $(python -c "import json; d=json.load(open('.claude-plugin/plugin.json')); [print(p) for p in d.get('commands',[])]"); do test -f "$f" && echo "OK: $f" || echo "MISSING: $f"; done`
- [ ] Every path in `agents` array resolves to an existing file — run: `for f in $(python -c "import json; d=json.load(open('.claude-plugin/plugin.json')); [print(p) for p in d.get('agents',[])]"); do test -f "$f" && echo "OK: $f" || echo "MISSING: $f"; done`
- [ ] `name` field matches directory name: `dev-workflows-governance`
- [ ] `version` field is valid semver (X.Y.Z format)
- [ ] `license` field matches LICENSE file (MIT)

## Rollback

1. `git checkout -- .claude-plugin/plugin.json`
2. If marketplace.json was also changed: `git checkout -- .claude-plugin/marketplace.json`
3. If already pushed: force-push is required (confirm with user first)

## Common failure modes

| Failure | Cause | Prevention |
|---|---|---|
| Plugin silently fails to load | Invalid JSON (trailing comma, missing quote) | Always run `python -m json.tool` after edit |
| Command not found after install | Path in `commands` array doesn't match actual file location | Verify every path resolves before committing |
| Agent not available | Path in `agents` array uses wrong relative path | Paths must start with `./` relative to plugin root |
| Marketplace install fails | `marketplace.json` name doesn't match `plugin.json` name | Keep both files in sync |
| Version conflict on upgrade | Version not bumped before push | Always bump version for any published change |
