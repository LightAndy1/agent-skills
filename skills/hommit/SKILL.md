---
name: hommit
description: Use when the user asks to commit changes (commit, commit this, commit changes, commit like human, separate by categories, /hommit) or stages work to commit.
license: MIT
compatibility: opencode, gemini, antigravity, claude
metadata:
  audience: developers
  workflow: git-commit
  version: "2"
---

# hommit

Default handler for bare `commit`. Takes precedence over `caveman-commit` unless the user explicitly asks for `caveman commit` or `/caveman-commit`.

## Workflow

### 1. Inspect before staging

```bash
git status --short
git diff --stat
git log --oneline -10
```

Read `git diff <file>` for modified files and inspect untracked files before staging. If the working tree is clean, stop and say `tree clean, nothing to commit`.

### 2. Group into category buckets

Split changes by category (1 commit per category present, usually 1 to 4 commits, max 5):

- `backend`: server, API, database, core logic
- `api-wiring`: bindings, client wrappers, shared schemas
- `frontend`: UI components, pages, client state
- `ui-tweak`: layout, icons, spacing, small visual fixes
- `fix`: isolated bugfix touching 1 or 2 spots
- `chore`: configs, CI, dependencies, build scripts, `.gitignore`
- `wip`: half finished work committed with a note to fix later

Rules:
- Never lump unrelated categories (such as backend and a CSS tweak) into one commit.
- If one file has two categories, split hunks noninteractively with `git apply --cached` from a patch. Never hang on interactive `git add -p`. If splitting is too messy, commit the file under its dominant category and mention it.
- If more than 5 categories exist, merge smaller related ones.

### 3. Stage and commit sequentially

In dependency order (`backend` -> `api-wiring` -> `frontend` -> `ui-tweak` -> `fix` -> `chore`):

```bash
git add <bucket-files>
git commit -m "<message>"
git show --stat HEAD
```

Never run `git add -A` into a single commit. Never push unless the user explicitly asks to push.

### 4. Message style

- Lowercase by default. Occasional all caps only for emphasis (`SMH`, `LOL`, `UGH`).
- 40 to 72 characters ideally, hard max 80. No body unless truly needed. No trailing period.
- No conventional prefixes (`feat:`, `fix(scope):`) or emoji, unless repo history already uses them; if it does, mix inconsistently like a human.
- Reference what actually changed. Never write generic filler like `update files` or `improve stuff`.
- Slang cap: at most 1 slang token (`lol`, `smh`, `ugh`, `oops`, `finally`) per message, in at most half of the commits, never repeated back to back.
- Light ESL slip in roughly 1 in 3 commits (at most 1 slip per message, keep readable): dropped article (`fix drag for window`), wrong preposition (`wire up preview on api`), or minor typo (`seperate`, `fixd`).

Good:
- `auth token refresh finally works`
- `wire up users fetch on api, forgot smh`
- `fix header padding, again`
- `oops broke sidebar state, fixd`
- `remove dead css nobody liked`

### 5. Verify and report

Run `git log --oneline -6` and `git status --short`. List each commit (hash, message, files), note any leftover dirty or skipped files, and ask before pushing.

## Edge cases

- **Secrets in tracked diff or staged files** (`*.pem`, `API_KEY`, tokens): stop, warn, do not commit.
- **Untracked secret or build files** (`.env*`, `*.pem`, `dist/`, `build/`): skip them, warn the user, and commit the rest unless told otherwise.
- **Merge conflict or rebase in progress**: stop and tell the user to resolve it first.
- **Commit hook failure** (such as `commitlint` rejecting format): report the hook error and ask whether to adapt the format. Never pass `--no-verify` unless asked.
- **"Commit normally"**: use clean normal messages while still splitting by category.

## Banned

- Single `git add -A && git commit -m "update"`
- `git push --force`, `git commit --amend` on pushed commits, or `git config` edits without explicit permission
- Empty commits
