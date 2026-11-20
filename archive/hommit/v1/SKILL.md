---
name: hommit
description: >
  Git commit grouped by category with messy human-like messages. Use when user says
  commit, commit this, commit changes, commit like human, weird commit msgs,
  separate by categories, /hommit. Auto-triggers when commit seen in prompt.
license: MIT
compatibility: opencode, gemini, antigravity, claude
metadata:
  audience: developers
  workflow: git-commit
---

## What I do

Run git commit like tired human at 2am, not AI bot. Split work by category into
2-4 small commits, write lowercase messy messages. This is default handler for
bare `commit`.

## When to use me

Use when prompt contains `commit` in any form: `commit`, `commit this`,
`commit changes`, `commit like human`, `weird commit msgs`,
`separate by categories`, `/hommit`.
Auto-triggers when staging changes or `commit` seen. Takes precedence over
`caveman-commit` on bare `commit`; `caveman-commit` needs explicit
`caveman commit` or `/caveman-commit`.

## Workflow

### 1. Inspect first, never blind-commit

```bash
git status --short
git diff --stat
git log --oneline -10
git diff <files>
```

No commit without reading diff. No guessing categories.

### 2. Group by category

Split changed files/hunks into human buckets. Typical buckets:

- `backend`: `*.go`, `backend/**`, API, db, logic
- `frontend`: `frontend/src/**`, `*.tsx`, `*.ts`, `*.css`
- `api-wiring`: glue files like `lib.ts`, generated bindings, `invoke<>` wrappers
- `ui-tweak`: drag regions, titlebar, icons, crumbs, small visual fixes
- `fix`: bugfix that touches 1-2 spots, gets own commit
- `chore`: configs, `wails.json`, CI, `.gitignore`, build scripts
- `wip`: broken halfway stuff human would still commit with "will fix later lol"

Rules:

- 1 category = 1 commit. Never lump backend + CSS tweak together.
- If one file has 2 categories (e.g. `App.tsx` has drag fix + breadcrumb UI), try hunk-split with `git add -p`. If too messy, commit whole file under dominant category and say so.
- Prefer 2-4 commits. More than 5 = squash, human wouldn't spam that much.
- Empty status = stop, say "tree clean, nothing to commit".

### 3. Stage + commit per bucket, sequentially

```bash
git add <bucket-files>
git commit -m "<human-msg>"
```

- One `git add` + `git commit` per bucket, in dependency order: backend → api-wiring → frontend → ui-tweak → chore.
- Verify each commit with `git show --stat HEAD` before moving on.
- Never `git add -A` + single commit. That defeats whole skill.
- Never push unless user said "push". Commit ≠ push.

### 4. Message style — human, not AI

Human rules:

- lowercase dominant. no caps start. occasional caps for emphasis only (SMH, LOL, UGH).
- short: 40-72 chars ideally, hard max 80. no body unless truly needed. human doesn't write essays.
- no conventional-commits prefix (`feat:`, `fix(scope):`), no emoji, unless repo already uses them — then mix inconsistently like human would (sometimes emoji, sometimes not).
- no period at end.
- first person / emotional ok: "lol", "smh", "ugh", "oops", "finally", "stupid", "again".
- slang cap: max 1 slang token per msg, max ~half the commits in a run carry slang.
  never stack (`lol smh` banned). never same slang twice in a row — rotate.
  most commits clean, no filler.
- ESL light touch: ~1 in 3 commits gets exactly 1 small slip, rest clean.
  never >1 slip per msg. types: dropped/wrong article (`fix drag for window`),
  wrong preposition (`wire up preview on api`), missed plural (`little caret thing`),
  one small typo (`seperate`, `fixd`, `recieve`) but keep readable.
  slip anywhere incl code words, but keep recognizable — never fake-gibberish.
- reference what changed concretely, not generic "update files" or "improve stuff".

Good (note: most clean, slang sparse, ESL light):

- `quicklook preview shit finally works`
- `wire up preview on api, forgot smh`
- `fix drag for window, again`
- `icons aligned, finally`
- `backend done, frontend still broken will fix later`
- `oops broke crumb, fixd`
- `remove dead css nobody liked`

Bad (AI smell, never do):

- `✨ feat(macos): implement native Quick Look preview with temp file handling`
- `Refactor breadcrumb navigation to improve UX and maintainability.`
- `Update multiple files to enhance functionality and fix issues.`
- `chore: bump deps, fix drag, add icons, update api (all in one)`

### Category → message templates

Pick, tweak, don't copy verbatim every time. Rotate slang — never same one twice in a row in a run:

- backend: `<thing> shit finally works` / `add <thing>, backend only`
- api-wiring: `wire up <fn> on api, forgot smh` / `hook up <x> to backend`
- frontend: `hook <x> into ui` / `<page> now calls <fn>`
- ui-tweak: `fix drag for <thing>, again` / `little <thing> for <place>`
- fix: `oops <what broke>, fixd` / `<bug> fixed`
- chore: `mess with <config> again` / `<file> stuff`

### 5. Verify + report terse

```bash
git log --oneline -6
git status --short
```

Report: list commits made (hash + msg + files). If tree still dirty, say what's left. Ask about push, don't auto-push.

## Edge cases

- Secrets in diff (`*.pem`, `.env`, `API_KEY`, token)? Stop, warn, don't commit.
- Generated files (`wailsjs/`, `dist/`, `build/`)? Skip unless user explicitly wants them. Mention skipped.
- Merge conflicts / rebase in progress? Stop, say fix that first.
- User says "commit normally"? Fall back to normal clean messages, still split by category.

## Anti-patterns

- One giant `git add -A && git commit -m "update"` — banned.
- AI-perfect grammar + emoji + scope on every commit — banned, that's bot fingerprint.
- `git push --force`, `commit --amend` on pushed commits, `git config` changes — never without explicit ask.
- Commit empty tree to "keep habit" — never.
