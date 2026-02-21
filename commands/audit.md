---
description: Run a governance audit on the current project. Analyses the stack, identifies high-risk operation types, and generates project-specific executable checklists in docs/checklists/. Run once when adding governance to a project, or when the stack changes significantly.
allowed-tools: Bash, Read, Edit, Glob, Grep
---

Run the governance-audit agent on this project.

Arguments: $ARGUMENTS

If a specific operation is provided (e.g. /audit stripe or /audit database), generate or update the checklist for that operation only.

If no argument is provided, run a full audit across all identified operation types.

The audit will:
1. Read project files to understand the stack, services, and deployment targets
2. Identify 3-6 operation types most likely to cause silent failures or hard-to-reverse damage
3. Write executable checklists to docs/checklists/
4. Update CLAUDE.md with references to the generated checklists

Output: docs/checklists/<operation>.md files, plus CLAUDE.md update.

Every checklist item names actual files, commands, and services found in this codebase. Generic advice is not produced.