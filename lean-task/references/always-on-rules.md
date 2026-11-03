# Always-on rules (paste into AGENTS.md)

Skills are loaded when the agent decides they are relevant. A rules file is read on every task, so it is more reliable for habits you want 100% of the time. Paste this block at the top of your project's `AGENTS.md` (Antigravity and most coding agents read it; if your tool wants another name, symlink: `ln -s AGENTS.md GEMINI.md`).

```markdown
## Working style: small tasks stay small
- Size the task first. If the request names the exact change (swap an image, edit text/value, rename, fix a typo), it is SMALL: max 6 tool calls, no new files.
- Never create analyze_/check_/inspect_/extract_/debug_ scripts in the repo. Use one-line shell commands or `python -c`.
- Do not analyze things I did not ask about. Use the asset or value I gave you.
- Change only the files the task needs. No refactors, no new dependencies, no new abstractions.
- Do not ask permission for obvious steps. Ask only when two interpretations give different results.
- Verify once with the cheapest check, then stop.
- Final reply: 2-4 lines. What changed, where, any caveat.
- If the task turns out bigger than it looked, say so in one line and give a 3-line plan.
```

## Prompt suffix for one-off use
Add this to any simple request: `Small task. Smallest possible change, no new files, no extra analysis, max 6 tool calls.`
