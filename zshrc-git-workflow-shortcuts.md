# Git workflow shortcuts

Daily git and repo cleanup aliases for zsh. Defined in `~/.zshrc`.

## Overview

| Alias | Purpose |
| ----- | ------- |
| **`git-pull-main`** | Sync the current repo with `origin/main` (fetch all, pull with rebase) |
| **`repo-clean`** | Clear Jest cache and delete merged local branches |

Both aliases run `clear` first so the terminal starts clean.

---

## `git-pull-main`

Fetch from all remotes, then pull `origin/main` with rebase.

```bash
git-pull-main
```

Equivalent to:

```bash
clear && git fetch --all && git pull origin main --rebase
```

**When to use:** start of a work session, before branching, or when you want the latest `main` without a merge commit.

**Note:** always pulls `origin main` — switch remotes or branch manually if the repo uses a different default.

---

## `repo-clean`

Clear Jest’s cache and remove local branches already merged into the current branch.

```bash
repo-clean
```

Equivalent to:

```bash
clear && npx jest --clearCache && git branch --merged | egrep -v "(^\*|master|main|v2|dev|staging)" | xargs git branch -d
```

**Protected branches** (never deleted): current branch (`*`), `master`, `main`, `v2`, `dev`, `staging`.

**When to use:** after merging PRs locally, when Jest behaves oddly after dependency changes, or periodic branch hygiene.

**Caution:** `git branch -d` only removes fully merged branches. Use `git branch -D` manually for force-delete.

---

## Source

Add to `~/.zshrc`:

```zsh
# Git workflow — sync main and clean merged branches
alias git-pull-main='clear && git fetch --all && git pull origin main --rebase'
alias repo-clean='clear && npx jest --clearCache && git branch --merged | egrep -v "(^\*|master|main|v2|dev|staging)" | xargs git branch -d'
```

Reload the shell after editing:

```bash
source ~/.zshrc
```
