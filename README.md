# dev-workflows-governance

A Claude Code plugin that adds a governance layer to any development workflow. Works with any stack, alongside or independently of [dev-workflows](https://github.com/shinpr/claude-code-workflows) and [dev-workflows-frontend](https://github.com/shinpr/claude-code-workflows).

## The Problem

Most Claude Code workflows end at "ready to commit." There is no mandatory cleanup stage, no human checkpoint before deployment, and no protection against the agent declaring victory prematurely.

This was identified in production: CC skipped the wrap-up stage because it treated it as "a rule to follow rather than a checklist to execute." The signoff block appeared with uncommitted junk, stale lint warnings, and an outdated BACKLOG.md. The human approved based on incomplete information.

**Rules get forgotten. Checklists get executed.**

## What This Adds

| Component | Purpose |
|---|---|
| `wrap-up` agent | TIDY stage — lint, tests, BACKLOG.md, clean commit. Reports STATUS: CLEAN or STATUS: BLOCKED |
| `signoff` agent | Human checkpoint — pre-flight checklist, stops and waits for "approved" |
| `governance-audit` agent | Analyses project stack, generates project-specific operation checklists |
| `/tidy` command | Invoke TIDY stage manually |
| `/signoff` command | Invoke signoff gate manually |
| `/audit` command | Run governance audit (full or targeted: `/audit stripe`) |
| `checklist-vs-rules` skill | Explains the pattern and how to apply it to your own CLAUDE.md |
| `anti-rationalization.json` | Stop hook that catches premature victory declarations |

## Installation
```
/plugin marketplace add francismiles1/dev-workflows-governance
/plugin install dev-workflows-governance@dev-workflows-governance
```

Restart your Claude Code session after installation.

## Workflow

Add these two stages to the end of your existing workflow:
```
... your existing workflow ...
    ↓
  /tidy        ← must report STATUS: CLEAN
    ↓
  /signoff     ← type "approved" to push and deploy
    ↓
  push + deploy
```

With dev-workflows:
```
/implement → /review → /tidy → /signoff → deploy
```

## Anti-Rationalization Hook

Copy the Stop hook from `hooks/anti-rationalization.json` into your `.claude/settings.json` to catch premature victory declarations at the end of every session.

## Governance Audit

Run `/audit` once on a new project to generate operation-specific checklists for your stack:
```
/audit              # full audit
/audit stripe       # target a specific operation
/audit database
```

Checklists are written to `docs/checklists/` and reference actual files, commands, and services in your codebase.

## Related

- [dev-workflows](https://github.com/shinpr/claude-code-workflows) — backend development workflows
- [metronome](https://github.com/shinpr/metronome) — detects shortcut-taking behaviour mid-task

## License

MIT