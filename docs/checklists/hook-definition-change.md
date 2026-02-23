# Hook Definition Change

## When to use
Any modification to `hooks/hooks.json` — changing the prompt text, altering rejection patterns, modifying the hook type, or adding new hooks.

**If done incorrectly:** Malformed JSON breaks Claude Code configuration for users who installed the plugin. Overly aggressive rejection patterns block legitimate completions. Overly permissive patterns defeat the purpose.

## Reversibility
`git checkout -- hooks/hooks.json` restores previous version. Users who have already installed the plugin will get the fix on next plugin update.

---

## Pre-flight

- [ ] Read current `hooks/hooks.json` — understand existing structure and patterns
- [ ] Identify what is changing: prompt text, hook type, rejection patterns, or structural changes
- [ ] Consider user impact: this file is copied verbatim into user settings

## Change steps

- [ ] Edit `hooks/hooks.json`
- [ ] Ensure top-level structure is `{"hooks": {"Stop": [...]}}` with optional `"description"` field
- [ ] Each hook group within `"Stop"` array has a `"hooks"` array
- [ ] Each hook has `"type": "prompt"` and a `"prompt"` string
- [ ] The prompt must include both rejection (`{"ok": false, "reason": "..."}`) and acceptance (`{"ok": true}`) response formats
- [ ] Rejection patterns must be specific enough to catch real rationalisation but not block legitimate completions
- [ ] If adding new patterns: list them explicitly in the prompt, don't use vague criteria
- [ ] Update README.md "Anti-Rationalization Hook" section if hook behaviour changed

## Verification

- [ ] Validate JSON syntax — run: `python -m json.tool hooks/hooks.json`
- [ ] Verify structure matches Claude Code plugin hook schema:
  - Top-level key: `"hooks"` (object)
  - `"hooks"` contains `"Stop"` (array)
  - Each element: object with `"hooks"` array
  - Each hook: `{"type": "prompt", "prompt": "<string>"}`
  — run: `python -c "import json; d=json.load(open('hooks/hooks.json')); assert 'hooks' in d; assert 'Stop' in d['hooks']; print('STRUCTURE OK')"`
- [ ] Prompt string contains no unescaped quotes that would break JSON — the `python -m json.tool` check covers this
- [ ] Prompt includes `{"ok": false, ...}` rejection format — run: `grep -c '"ok": false' hooks/hooks.json` (should be >= 1)
- [ ] Prompt includes `{"ok": true}` acceptance format — run: `grep -c '"ok": true' hooks/hooks.json` (should be >= 1)
- [ ] README.md hook section matches current behaviour — read and compare

## Rollback

1. `git checkout -- hooks/hooks.json`
2. If already pushed: push a follow-up commit with the fix
3. Notify users in release notes if a broken hook was published — they need to update their `.claude/settings.json`

## Common failure modes

| Failure | Cause | Prevention |
|---|---|---|
| User's Claude Code breaks entirely | Invalid JSON copied into settings.json | Always validate with `python -m json.tool` before committing |
| All agent responses rejected | Overly broad rejection patterns in prompt | Test with realistic completion examples before publishing |
| Rationalisation not caught | Prompt patterns too specific or too narrow | Include explicit examples of each rejection pattern |
| Hook silently ignored | Wrong hook type (not `"Stop"`) or wrong structure | Verify structure matches Claude Code's expected schema |
| Prompt injection via crafted output | Agent output designed to bypass the hook | Keep rejection criteria based on behaviour patterns, not specific strings |
