# Scatter — Development Guide

This file is for Claude sessions working on the Scatter codebase, not for end users.

## Architecture

Scatter is a Claude Code skill pack installed at `~/.claude/skills/scatter/` (user-level) or `.claude/skills/scatter/` (project-level). The orchestrator (`agents/scatter.md`) runs in tmux pane 0 and spawns team leads in separate panes. Each team lead is an independent `claude` process — not an in-process subagent.

```
User → Orchestrator (pane 0)
         ├─ bin/spawn-lead → Team Lead A (pane 1, own claude session)
         ├─ bin/spawn-lead → Team Lead B (pane 2, own claude session)
         └─ bin/station    → monitors all panes
```

The orchestrator is **forbidden** from using TeamCreate/Agent/SendMessage. All team creation goes through `bin/spawn-lead` via the Bash tool. This is the core design decision — separate processes give each team its own context window and let them run in parallel without contention.

## Key files

- `agents/scatter.md` — The orchestrator's system prompt. This is the brain. It defines how tasks are parsed, routed, and dispatched.
- `bin/spawn-lead` — Creates a tmux pane with a new `claude` session. Handles worktree setup, context injection, and ledger recording. Writes the prompt to a temp file (never shell-interpolated) and generates a launcher script.
- `bin/station` — Team management CLI: list, capture, kill, cleanup, sweep, resume, merge. Reads tmux state and the session ledger.
- `bin/context-pack` — Assembles context from station.toml + vault briefing + CLAUDE.md + Claude memory into a single block injected into team lead prompts.
- `bin/vault` — Obsidian vault adapter. Reads journal entries, generates briefings, searches vaults. All scoped per-project via station.toml.
- `setup` — Root-level installer: creates skill symlinks, adds shell aliases.
- `bin/setup` — Legacy alias installer (kept for backward compat).
- `skills/spawn-team/SKILL.md` and `skills/station/SKILL.md` — Skill definitions that tell Claude Code how to discover and use the shell scripts.
- `.claude-plugin/plugin.json` — Plugin manifest registering agents and skills.
- `station.example.toml` — Example config. Users copy to `station.toml` (gitignored) with their own projects/repos/vaults.

## How spawn-lead works (the tricky part)

1. Validates args, resolves `--cwd` to absolute path
2. If `--worktree`, creates a git worktree at `<repo>/.worktrees/<team-name>/`
3. If `--context`, runs `context-pack` to prepend vault/project context to the prompt
4. Writes prompt to a temp file (`/tmp/scatter-prompt.XXXXXX.txt`) — this avoids shell escaping issues with complex prompts
5. Generates a launcher script (`/tmp/scatter-spawn-launcher.XXXXXX.sh`) with baked-in env vars that reads the prompt file at runtime
6. Runs the launcher in a new tmux pane via `tmux split-window`
7. The launcher runs `claude --permission-mode <mode> --teammate-mode in-process "$(cat prompt-file)"`, then writes completion status to `/tmp/scatter-status/<name>` and appends to the durable ledger at `~/.local/share/scatter/ledger.jsonl`

## Gotchas

- **Prompt is never embedded in shell.** The prompt goes to a temp file and is `cat`'d at runtime. This is intentional — prompts contain quotes, newlines, backticks, and all kinds of shell-hostile characters. Don't try to inline them.
- **The launcher script cleans up after itself.** It traps EXIT to remove both the launcher and prompt temp files.
- **Worktree cleanup order matters.** `station cleanup` must get the pane's cwd before killing it, because the cwd tells us where the worktree lives. Kill first → lose the path.
- **The ledger is append-only.** `~/.local/share/scatter/ledger.jsonl` records spawn, done, kill, sweep, and merge events. `station resume` replays it to reconstruct state. Don't try to edit or truncate it.
- **tmux session name defaults to `command-station`.** Override with `$TMUX_SESSION` env var.
- **Pane layout:** First team lead splits horizontally (left/right). Subsequent ones stack vertically on the right side.

## Testing changes

There are no automated tests. To test manually:

1. You need tmux running: `tmux new-session -s command-station`
2. Test `bin/spawn-lead` with a simple prompt: `bin/spawn-lead --name test-team --cwd /tmp --prompt "Say hello and exit"`
3. Test `bin/station list`, `station capture test-team`, `station kill test-team`
4. For worktree testing, use a disposable git repo
5. Vault features need a `station.toml` with valid vault paths

## Installation model

Scatter follows the gstack-style install: `git clone` into `~/.claude/skills/scatter/`, then `./setup` creates symlinks and shell aliases. Project-level installs copy the files into `.claude/skills/scatter/` (no submodule).

## Directory structure

```
setup                        — Installer: symlinks + shell aliases
agents/
  scatter.md                 — Orchestrator system prompt
skills/
  spawn-team/SKILL.md        — Skill: spawn a team lead
  station/SKILL.md           — Skill: monitor/manage teams
bin/
  spawn-lead                 — Create tmux pane + launch claude
  station                    — Team management CLI
  context-pack               — Assemble context for team lead prompts
  vault                      — Obsidian vault adapter
  setup                      — Legacy alias installer
.claude-plugin/
  plugin.json                — Plugin manifest (agent + skill registration)
  marketplace.json           — Marketplace metadata
station.example.toml         — Example config (users copy to station.toml)
```
