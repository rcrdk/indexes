# GitHub CLI — Multiple Accounts

Two independent `gh` logins on `github.com` — personal and work — kept apart by separate config directories (`GH_CONFIG_DIR`) and exposed as zsh aliases.

| Source | Alias | Config dir | Login |
| ------ | ----- | ---------- | ----- |
| Personal GitHub | `gh-personal` | `~/.config/gh` (gh default) | `rcrdk` |
| Work GitHub (MySide) | `gh-myside` | `~/.config/gh-myside` | `rcrdkw` |

Pairs with [Git SSH — multiple accounts (macOS)](./git-ssh-multiple-accounts-macos.md): SSH handles `git` remotes, `GH_CONFIG_DIR` handles the `gh` API/PR layer.

---

## 1. Personal profile

The personal account uses gh's default config dir, so a plain `gh auth login` sets it up:

```bash
gh auth login
```

Log in with **`rcrdk`**.

Prefer a dedicated directory instead of the default? Create it and point the alias in step 3 at it:

```bash
mkdir -p ~/.config/gh-personal
GH_CONFIG_DIR=~/.config/gh-personal gh auth login
```

---

## 2. Work profile (MySide)

```bash
mkdir -p ~/.config/gh-myside
GH_CONFIG_DIR=~/.config/gh-myside gh auth login
```

Log in with the company account (**`rcrdkw`**).

---

## 3. Aliases

Add to `~/.zshrc`:

```bash
alias gh-personal='GH_CONFIG_DIR=$HOME/.config/gh gh'
alias gh-myside='GH_CONFIG_DIR=$HOME/.config/gh-myside gh'
```

Reload:

```bash
source ~/.zshrc
```

---

## Usage

Check which account each profile is on:

```bash
gh-personal auth status
gh-myside auth status

gh-personal api user --jq .login   # rcrdk
gh-myside api user --jq .login     # rcrdkw
```

Everyday commands — same `gh` surface, different identity:

```bash
gh-personal pr create
gh-myside pr create

gh-personal repo list
gh-myside repo list
```

---

## Using the aliases from agents

Aliases live in `~/.zshrc`, so non-interactive shells do not see them. Wrap calls in an interactive zsh:

```bash
zsh -ic 'gh-personal pr view --json number,url,title,state'
```

Rules that enforce this:

| Scope | Rule | Requires |
| ----- | ---- | ------- |
| Personal repos (agent-kit consumers) | [`github-cli.mdc`](https://github.com/rcrdk/agent-kit/blob/main/rules/github-cli.mdc) | `gh-personal` only, login `rcrdk` |
| Work repos | `/hero-fix-pr-comments` (Cursor command) | `gh-myside` only, login `rcrdkw` |

Never expand the alias back to `GH_CONFIG_DIR=… gh` in agent flows, and never fall back to plain `gh` — the wrong config silently posts as the wrong account.

---

## Notes

- Both profiles authenticate against the same host (`github.com`); only the config dir differs, so tokens, hosts, and aliases stay isolated.
- `gh auth switch` is not needed — the alias picks the identity.
- If a command hits 404 on a repo you can see in the browser, you are almost certainly on the other profile: check `api user --jq .login` first.
