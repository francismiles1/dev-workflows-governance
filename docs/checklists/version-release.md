# Version Release

## When to use
Any change that will be pushed to the remote repository — bumping version for a new release, preparing a tagged release, or publishing to the marketplace.

## Risk level: MEDIUM
Incorrect version bumps cause installation conflicts. Forgetting to bump means users don't get updates. Desync between `plugin.json` and `marketplace.json` breaks marketplace discovery.

## Reversibility
Version can be amended in a follow-up commit. If a tag was pushed, it must be deleted remotely (`git push --delete origin <tag>`). If users have already installed the incorrect version, they must manually update.

---

## Pre-flight

- [ ] Determine change type: **patch** (fix), **minor** (new feature/checklist), or **major** (breaking change to agent behaviour or plugin structure)
- [ ] Read current version — run: `python -c "import json; print(json.load(open('.claude-plugin/plugin.json'))['version'])"`
- [ ] Check `marketplace.json` version matches — run: `python -c "import json; print(json.load(open('.claude-plugin/marketplace.json'))['version'])"`
- [ ] Review all changes since last version bump — run: `git log --oneline`

## Change steps

- [ ] Bump `version` in `.claude-plugin/plugin.json` following semver:
  - Bug fix / typo / minor doc update → patch (e.g. 1.0.0 → 1.0.1)
  - New agent, command, skill, or checklist → minor (e.g. 1.0.0 → 1.1.0)
  - Breaking change to frontmatter format, renamed files, removed components → major (e.g. 1.0.0 → 2.0.0)
- [ ] Bump `version` in `.claude-plugin/marketplace.json` to the same value
- [ ] Update README.md if the change is user-facing (new features, changed behaviour)
- [ ] Update CLAUDE.md if structure has changed

## Verification

- [ ] Both JSON files are valid — run: `python -m json.tool .claude-plugin/plugin.json && python -m json.tool .claude-plugin/marketplace.json`
- [ ] Versions match — run: `python -c "import json; p=json.load(open('.claude-plugin/plugin.json'))['version']; m=json.load(open('.claude-plugin/marketplace.json'))['version']; print(f'plugin: {p}, marketplace: {m}'); assert p==m, 'VERSION MISMATCH'"`
- [ ] Version is strictly greater than previous — compare with: `git show HEAD:.claude-plugin/plugin.json | python -c "import json,sys; print(json.load(sys.stdin)['version'])"` (previous) vs current
- [ ] All other checklists for changed components have been completed (manifest, agent, hook changes)

## Rollback

1. If not yet pushed: amend commit with corrected version
2. If pushed but no tag: push a follow-up commit with correct version
3. If tag was pushed: `git tag -d <tag> && git push --delete origin <tag>`, then re-tag

## Common failure modes

| Failure | Cause | Prevention |
|---|---|---|
| Users don't receive update | Version not bumped before push | Always bump version for any published change |
| Marketplace shows stale description | marketplace.json not updated alongside plugin.json | Always update both files together |
| Version conflict on install | Version bumped but not following semver | Use strict X.Y.Z format, always increment |
| plugin.json and marketplace.json desync | One file updated, other forgotten | Verify both versions match before committing |
