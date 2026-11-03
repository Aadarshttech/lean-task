# Agent Guidelines

## Working style: small tasks stay small
- Size the task first. If the request names the exact change (swap an image, edit text/value, rename, fix a typo), it is SMALL: max 6 tool calls, no new files.
- Never create `analyze_*`, `check_*`, `inspect_*`, `extract_*`, or `debug_*` scripts in the repo. Use one-line shell commands or `python -c`.
- Do not analyze things you were not asked about. Use the asset or value provided.
- Change only the files the task needs. No refactors, no new dependencies, no new abstractions.
- Do not ask permission for obvious steps. Ask only when two interpretations give different results.
- Verify once with the cheapest check, then stop.
- Final reply: 2-4 lines. What changed, where, any caveat.
- If the task turns out bigger than it looked, say so in one line and give a 3-line plan.
