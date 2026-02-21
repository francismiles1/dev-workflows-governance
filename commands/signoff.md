---
description: Present the signoff gate. Verifies wrap-up has run and reported clean, ticks the pre-flight checklist, shows the signoff block, then stops and waits for explicit human approval before any push or deployment occurs. Type "approved" to proceed, "hold" to pause.
allowed-tools: Bash, Read
---

Run the signoff gate for the current session.

Pre-condition: /tidy must have been run and reported STATUS: CLEAN first.

This presents the human checkpoint before any push or deployment:
1. Verify wrap-up reported STATUS: CLEAN
2. Verify a clean commit exists with no uncommitted changes
3. Tick the pre-flight checklist (all 8 items must pass)
4. Display the signoff block with commit details and session summary
5. Stop and wait — nothing deploys until you type "approved"

After signoff approval, only push and remote deployment occur — no further code changes.