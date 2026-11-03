---
name: lean-task
description: Keeps a coding agent fast and minimal on simple, well-defined tasks. Use this on EVERY coding request that sounds small - replace/swap an image or asset, change text, color, size, or a config value, rename something, fix a typo, add a field or a log line, move a file, bump a version, tweak a style - even if the user does not say "simple". Stops the agent from writing scratch/analysis scripts, inspecting things it was not asked about, refactoring, or touching extra files. If the user complains the agent is overthinking, overengineering, or slow, use this immediately.
---

# Lean Task

Simple tasks fail in one way: the agent treats a 2-minute job like a research project. It writes `analyze_*.py`, `check_*.py`, `inspect_*.py` scripts, studies things nobody asked about, and ends up with 12 changed files for a one-file change. Every extra step costs time, tokens, and review effort, and adds risk of breaking something.

Your job on a small task: make the smallest correct change, check it once, report in a few lines, stop.

## Step 1: Size the task (do this silently, in one thought)

| Size | Looks like | Budget |
|------|-----------|--------|
| **S** | One clear change in 1-2 files: swap an image, edit text, change a value, rename, fix a typo | **max 6 tool calls, 0 new files in the repo** |
| **M** | A feature or bug touching a few files, needs some reading | Short plan (3 lines), then implement |
| **L** | Architecture change, new system, ambiguous scope | Ask or plan first |

If the request names the exact thing and the exact change, it is **S**. When unsure between S and M, start as S. Escalate only if the first look proves otherwise.

## Step 2: The S-task loop

1. **Locate** - one search to find where the thing lives (`grep -rn "name"`, `ls`, or `git ls-files | grep x`). Do not read the whole project.
2. **Read** only the lines you will edit, plus their immediate surroundings.
3. **Edit** - make the change directly.
4. **Verify once** - the cheapest check that proves it worked (see below).
5. **Report** - 2-4 lines: what changed, which file(s), anything the user must do. Then stop.

## Hard rules for S tasks

These exist because each one is a known time sink:

- **No scratch scripts.** Do not create `analyze_`, `check_`, `inspect_`, `extract_`, `debug_` files. If you truly need to look at something, use a one-line shell command or `python -c "..."`. Never leave files in the repo that the user did not ask for.
- **Do not analyze content you were not asked about.** Replacing an image does not require detecting its colors, sky, birds, or components. The user already chose the new image; use it.
- **Match, don't investigate.** To keep the layout intact, read the old asset's dimensions and format with one command (`file old.png new.png` or `identify`) and resize/convert the new one to match only if they differ.
- **Touch only the files the task needs.** No drive-by refactors, renames, formatting changes, dependency additions, or "while I'm here" fixes. Mention such things in one line at the end instead.
- **No new abstractions.** No helper, wrapper, config layer, or component unless the user asked. Hardcode the simple thing.
- **Do not ask permission for obvious steps.** Ask only if there are two truly different interpretations that change the result (e.g. two images named logo.png in different places and no way to tell which). Otherwise pick the most likely, say so in one line, and proceed.
- **Do not re-verify what you already verified.** One check, then done.

## Cheapest useful verification

Pick one, not all:
- Text/value change: `grep` the new value, or `git diff --stat`.
- Asset swap: confirm the path referenced in code exists and the file opens (`file path`), and the old reference was updated or the filename kept the same.
- Code change: run the project's existing lint/type/test command for that file only, if one exists. Do not write new tests for a one-line change.
- UI change: only start the dev server or take screenshots if the user asked to see it or the change cannot be judged otherwise.

## Recipes for common small tasks

**Replace an image/asset** (the classic over-engineered one)
1. `grep -rn "old-filename"` to find where it is used and where the file lives.
2. `file old new` to compare format and size. If they differ, convert/resize new to match with one command (ImageMagick `magick`/`convert`, or `sips` on Mac), otherwise skip.
3. Copy the new file over the old path (same filename keeps every reference working). Only edit code if the user wants a new filename.
4. Confirm with `git status --short` that only the asset (and maybe one code line) changed. Done.

**Change text/color/size/config value**: grep, edit that line, grep again to confirm.

**Rename**: use the editor/IDE-style replace or `grep -rl` + `sed`, then grep for leftovers.

**Add a field/log/prop**: read the neighboring code, copy its pattern exactly, add the line.

## When to escalate out of S

Stop and say so in one line if, after the first look, you find: the change needs 3+ files you did not expect, behavior depends on unclear business rules, or something would be deleted or overwritten that the user did not mention. Then switch to M: give a 3-line plan and continue (or ask one specific question). Escalating is fine. Silently turning a small task into a big one is not.

## Final report format

```
Done. Replaced <what> in <file(s)>.
<One line on any caveat, e.g. resized from 1200x800 to 1000x600 to match the old one.>
```

No long summaries, no listing of every command run, no restating the request.

## Self-check before you finish

- Did I create any file the user did not ask for? Delete it.
- Is the diff bigger than the request? Revert the extras.
- Could the user have done this review in under 30 seconds? If not, simplify.
