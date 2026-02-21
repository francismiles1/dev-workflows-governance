---
name: checklist-vs-rules
description: Apply when setting up workflow governance, writing CLAUDE.md instructions, designing agent behaviour, or deciding how to enforce development standards. Explains why rules fail in agentic workflows and how checklists fix the problem. Use when instructions are being skipped, when wrap-up or signoff stages are treated as optional, or when an agent is rationalising incomplete work.
---

# Checklists vs Rules

## The Core Problem

Rules get forgotten. Checklists get executed.

When an instruction says "always run wrap-up before signoff", it is a rule. Rules exist in the agent context as a general principle. Under pressure — a long session, a complex task, an eager-to-please completion instinct — rules get skipped. The agent treats them as suggestions, not gates.

When an instruction is a checklist, each item is a discrete verifiable action. The agent cannot tick an item without performing it. The checklist cannot be "done" until every box is marked.

This distinction was identified in production: an agent skipped the wrap-up stage because it "treated it as a rule to follow rather than a checklist to execute." The signoff block appeared with uncommitted junk, stale lint warnings, and an outdated BACKLOG.md. The human approved based on incomplete information.

## The Rationalisation Problem

Agents rationalise incomplete work. Common patterns:

- "These test failures are pre-existing" (without verifying)
- "The lint warnings are minor" (without fixing them)
- "BACKLOG.md does not need updating" (without checking)
- "This file can be cleaned up later" (later never comes)

Rules do not stop this. A checklist item `[ ] Lint: zero warnings — run: npm run lint` cannot be ticked without running the command and seeing zero output.

## How to Apply This

### In CLAUDE.md

Replace rule paragraphs with numbered checklists:

**Before (rule):**
```
Always run wrap-up before signoff. Make sure lint is clean and tests pass.
```

**After (checklist):**
```
## Before Signoff — Mandatory Checklist
- [ ] Junk files removed
- [ ] Lint: zero warnings (npm run lint)
- [ ] Tests: N passed, 0 failed
- [ ] BACKLOG.md updated
- [ ] No secrets staged
- [ ] Clean commit created
```

### In Agent Definitions

Structure agent tasks as checklists with explicit done-when criteria:

**Before (rule-style):**
```
Review the code for quality issues and fix them.
```

**After (checklist-style):**
```
## Quality Review Checklist
- [ ] Run lint — fix all warnings before continuing
- [ ] Run tests — all must pass
- [ ] Check for unused imports
- [ ] Verify no debug statements left in code
- [ ] Confirm no hardcoded values that should be config

Done when: all items ticked, verified by running the commands above.
```

### In Operation Checklists

For high-risk operations (schema changes, payment config, infrastructure), create dedicated checklist files with pre-flight steps, specific change steps naming actual files and commands, post-change verification, and rollback procedure.

## The Private Memory Trap

Agents can create files in private memory directories (`~/.claude/memory/`) that are invisible to you, not version-controlled, and lost at session end. The agent believes the file exists. You never see it.

Rule: "Create all files in the project directory" — easily forgotten.

Checklist item: `[ ] All files created this session exist in project tree — verify with: git status` — cannot be ticked without running the check.

## Summary

| Rules | Checklists |
|---|---|
| Stated as principles | Stated as discrete actions |
| Forgotten under pressure | Cannot be skipped without explicit omission |
| Completion is ambiguous | Completion is verifiable |
| Fail silently | Failure is visible |
| "Always do X" | `[ ] Do X — verify with: <command>` |

When writing any governance instruction, ask: can an agent tick this item without actually doing it? If yes, it is a rule. Rewrite it as a verifiable action with a specific command or observable output.