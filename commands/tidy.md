---
description: Run the TIDY stage manually. Removes junk files, verifies lint, checks tests, updates BACKLOG.md, and creates a clean commit. Must complete with STATUS: CLEAN before /signoff can be used.
allowed-tools: Bash, Read, Edit, Glob, Grep
---

Run the wrap-up agent to bring the repository to a clean, committable state.

This performs the mandatory TIDY stage:
1. Remove junk and temporary files
2. Verify no files were created in private memory directories
3. Run lint — must be zero warnings
4. Run tests — report pass/fail count
5. Update BACKLOG.md with completed work and new debt
6. Review staged changes for secrets or unintended files
7. Create a clean commit

The wrap-up agent will report STATUS: CLEAN or STATUS: BLOCKED with specifics.

Do not proceed to /signoff until STATUS: CLEAN is confirmed.