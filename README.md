# 📇 Indexes

Personal catalog of tools, configs, and workflows I use day to day. Each entry links upstream — this repo is the map, not the source.

---

## 🤖 AI Tools

Shared agent config for rcrdk TypeScript/React repos lives in **[agent-kit](https://github.com/rcrdk/agent-kit)** — Cursor/Claude rules (`.mdc`), `/rcrdk-*` slash commands, bundled **skills** (`SKILL.md`), bootstrap script, and docs for the wider stack (Codebase Memory MCP, Superpowers, Karpathy Guidelines).

| Resource | Description |
| -------- | ----------- |
| [agent-kit](https://github.com/rcrdk/agent-kit) | Rules (`rules/shared/` + `rules/<project>/` + `rules/required/`), commands, bundled `skills/`, `setup-consumer.mjs`, templates |
| [agent-kit README](https://github.com/rcrdk/agent-kit/blob/main/README.md) | Quick start, slash commands, skills wiring, ecosystem, consumer layout |

**Slash commands** (all repos that vendor agent-kit):

| Command | Purpose |
| ------- | ------- |
| `/rcrdk-setup-agent-kit` | Bootstrap or refresh agent-kit — submodule add/update, scripts, gitignore, symlinks |
| `/rcrdk-index-codebase` | Index/reindex with Codebase Memory MCP |
| `/rcrdk-commit-unstaged` | Small Conventional Commits from unstaged/untracked changes |
| `/rcrdk-review-rules` | Review branch diff against project rules |
| `/rcrdk-fix-tests` | Fix broken tests only |

**Skills** — symlinked into `.cursor/skills` and `.claude/skills` via `pnpm setup:agents`:

| Skill | Source | Notes |
| ----- | ------ | ----- |
| [Karpathy Guidelines](https://github.com/rcrdk/agent-kit/blob/main/skills/karpathy-guidelines/SKILL.md) | Bundled in agent-kit | Behavioral guidelines — minimal diffs, verifiable success criteria |
| [Superpowers](https://github.com/obra/superpowers) | Optional submodule (`.agents/superpowers`) | Brainstorming, TDD, planning, subagent workflows — used by saas-template |
| Payment / marketing / design skills | Optional submodules under `.agents/` | Asaas, AbacatePay, marketingskills, emil-skills, etc. — saas-template only |

**Consumers** — vendored at `.agents/agent-kit`, symlinks via `pnpm setup:agents`:

| Repo | Project key | Skills layout | Notes |
| ---- | ----------- | ------------- | ----- |
| [rcrdk/utils](https://github.com/rcrdk/utils) | `utils` | `agents/skills/` | Rules, commands, bundled Karpathy skill |
| [rcrdk/saas-template](https://github.com/rcrdk/saas-template) | `saas-template` | `.agents/skills/` | agent-kit + skill submodules (Superpowers, Asaas, AbacatePay, …) under `.agents/` |

Edit rules, commands, and bundled skills in **agent-kit**, not in consumer repos. Bump the submodule SHA in each consumer after agent-kit changes.

---

## 🚀 Templates

App starters built on the same agent stack.

| Guide | Description |
| ----- | ----------- |
| [saas-template overview](https://github.com/rcrdk/saas-template/blob/main/README.md) | Next.js 16 SaaS template — feature-based `src/` layout, Vitest, Conventional Commits |
| [Project docs](https://github.com/rcrdk/saas-template/blob/main/docs/README.md) | Architecture, features, ADRs, AI workflow |
| [Agent setup](https://github.com/rcrdk/saas-template/blob/main/README-AGENTS.md) | agent-kit + skill submodules, symlink layout, `pnpm setup:agents` |
| [Agent guide](https://github.com/rcrdk/saas-template/blob/main/AGENTS.md) | Conventions, security, commands, Next.js docs index |
| [Rules index](https://github.com/rcrdk/saas-template/blob/main/agents/README.md) | Symlinks to agent-kit — includes saas-only overrides (docs, security, file-size-limits) |
| [Developer experience](https://github.com/rcrdk/saas-template/blob/main/README-DX.md) | ESLint, Prettier, Husky, commitlint, release-please |

---

## 🧰 Utilities

Day-to-day helpers from [rcrdk/utils](https://github.com/rcrdk/utils) — TS/React utilities, scripts, and DX tooling. Agent rules come from [agent-kit](https://github.com/rcrdk/agent-kit) (not defined in utils).

| Guide | Description |
| ----- | ----------- |
| [Overview](https://github.com/rcrdk/utils/blob/main/README.md) | Next.js 16 shell, scripts, structure, conventions |
| [Util functions](https://github.com/rcrdk/utils/blob/main/src/utils/README.md) | Pure helpers by domain — `groupBy`, `generateSlug`, `cn`, authenticated server action wrappers |
| [Components](https://github.com/rcrdk/utils/blob/main/src/components/README.md) | Shared React components — conventions, index, and usage (e.g. polymorphic `PolymorphicComponent` with an `as` prop) |
| [Hooks](https://github.com/rcrdk/utils/blob/main/src/hooks/README.md) | Shared React hooks — debounce, storage, observers, geolocation, IndexedDB, and more |
| [Lib](https://github.com/rcrdk/utils/blob/main/src/lib/README.md) | Third-party integrations — Auth.js, Sentry, and TanStack Query wrappers |
| [Auth](https://github.com/rcrdk/utils/blob/main/src/lib/auth/README.md) | Google OAuth setup, session helpers, and protected action integration |
| [Sentry](https://github.com/rcrdk/utils/blob/main/src/lib/sentry/README.md) | Error reporting and Sentry SDK configuration |
| [Config](https://github.com/rcrdk/utils/blob/main/src/config/README.md) | Shared constants — auth route paths, IndexedDB stores, TanStack Query defaults |
| [Developer experience](https://github.com/rcrdk/utils/blob/main/README-DX.md) | ESLint, Prettier, Husky, commitlint, TypeScript |
| [Agent setup](https://github.com/rcrdk/utils/blob/main/README-AGENTS.md) | agent-kit submodule, symlinks, and `agents/` layout |
| [Agent guide](https://github.com/rcrdk/utils/blob/main/AGENTS.md) | Conventions, commands, Next.js docs index |
| [Rules index](https://github.com/rcrdk/utils/blob/main/agents/README.md) | Symlinks to agent-kit — edit upstream, not in utils |
| [Editor setup](https://github.com/rcrdk/utils/blob/main/.vscode/README.md) | VS Code / Cursor — extensions, aliases, Tailwind IntelliSense |

---

## ⚙️ Workflows

Shell aliases, Git SSH setup, and other local dev workflows documented in this repo.

| Guide | Description |
| ----- | ----------- |
| [jest-test shortcut](./zshrc-jest-test-shortcut.md) | zsh helper for running Jest — paths, name patterns, and snapshot updates |
| [Git workflow shortcuts](./zshrc-git-workflow-shortcuts.md) | zsh aliases — sync `origin/main` with rebase, clear Jest cache, prune merged branches |
| [Git SSH — multiple accounts (macOS)](./git-ssh-multiple-accounts-macos.md) | Separate SSH keys for personal and work GitHub accounts |
