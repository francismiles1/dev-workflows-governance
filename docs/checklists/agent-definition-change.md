# Agent or Command Definition Change

## When to use
Any modification to files in `agents/` or `commands/` — changing frontmatter fields, altering agent instructions, modifying command descriptions, or adding/removing agent/command files.

## Risk level: HIGH
Broken frontmatter causes agents or commands to fail to load without clear error messages. Missing required fields (`name`, `description`) mean the component is silently unavailable. Instruction changes alter agent behaviour for every user.

## Reversibility
`git checkout -- agents/<file>` or `git checkout -- commands/<file>` restores previous version. Behavioural changes from instruction edits cannot be "rolled back" for users who have already acted on incorrect agent output.

---

## Pre-flight

- [ ] Read the file being changed — understand current frontmatter and instructions
- [ ] Identify blast radius: does this agent/command reference other agents? Does README.md describe its behaviour?
- [ ] If adding a new file: confirm the path will be added to `plugin.json` (see `plugin-manifest-change.md`)

## Change steps — Agents (`agents/*.md`)

- [ ] Frontmatter must include `name` field — verify present after edit
- [ ] Frontmatter must include `description` field — verify present after edit
- [ ] If agent name changed: update the corresponding path reference in `.claude-plugin/plugin.json`
- [ ] If agent behaviour changed: update README.md section describing this agent
- [ ] If agent output format changed (e.g. wrap-up STATUS line): check all agents that consume this output
  - `signoff.md` depends on wrap-up reporting `STATUS: CLEAN`
  - `governance-audit.md` is independent

## Change steps — Commands (`commands/*.md`)

- [ ] Frontmatter must include `description` field — verify present after edit
- [ ] `allowed-tools` field lists only tools the command actually needs
- [ ] If command name changed: update the path reference in `.claude-plugin/plugin.json`
- [ ] If command behaviour changed: update README.md section describing this command

## Change steps — Skill (`skills/checklist-vs-rules/SKILL.md`)

- [ ] Frontmatter must include `name` field — verify present after edit
- [ ] Frontmatter must include `description` field — verify present after edit

## Verification

- [ ] Frontmatter is valid YAML between `---` delimiters — visually inspect opening and closing `---`
- [ ] All agent files have `name` and `description` — run: `for f in agents/*.md; do echo "=== $f ==="; head -5 "$f"; done`
- [ ] All command files have `description` — run: `for f in commands/*.md; do echo "=== $f ==="; head -5 "$f"; done`
- [ ] SKILL.md has `name` and `description` — run: `head -5 skills/checklist-vs-rules/SKILL.md`
- [ ] plugin.json paths still resolve — run: `python -m json.tool .claude-plugin/plugin.json`
- [ ] README.md descriptions match current agent/command behaviour — read and compare
- [ ] No broken cross-references between agents (e.g. signoff referencing wrap-up output format)

## Rollback

1. `git checkout -- agents/<file>` or `git checkout -- commands/<file>`
2. If plugin.json was updated: `git checkout -- .claude-plugin/plugin.json`
3. If README.md was updated: `git checkout -- README.md`

## Common failure modes

| Failure | Cause | Prevention |
|---|---|---|
| Agent silently unavailable | Missing `name` field in frontmatter | Always verify frontmatter after edit |
| Command doesn't appear in `/` menu | Missing `description` field in frontmatter | Check all required frontmatter fields |
| Signoff gate never opens | wrap-up output format changed, signoff can't detect STATUS: CLEAN | Test wrap-up → signoff flow after any wrap-up change |
| Agent loads but behaves wrong | Instruction text changed without updating README | Always update README when changing agent behaviour |
| Duplicate agent names | Two agent files with same `name` frontmatter | Grep for name across all agent files before adding |
