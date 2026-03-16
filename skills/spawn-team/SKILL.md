---
name: spawn-team
version: 0.2.0
description: "Spawn a Claude Code team lead in a tmux split pane using bin/spawn-lead. MUST be used instead of TeamCreate/Agent when the user says: 'spawn a team', 'use spawn-lead', 'create a team for X', 'start a team on X', 'spin up agents in tmux', or any request to run parallel agent work in separate tmux panes. This skill runs spawn-lead which creates an independent Claude Code session in its own pane — NOT an in-process subagent."
---

# Spawn Team

Spawns a new Claude Code team lead in a tmux pane to the right of the Scatter command station.

## Usage

Run the `spawn-lead` script from this plugin's `bin/` directory:

```bash
<plugin-bin>/spawn-lead \
  --name "<team-name>" \
  --cwd "<absolute-path-to-repo>" \
  --prompt "<self-contained instructions for the team lead>" \
  [--worktree "<branch-name>"] \
  [--permission-mode acceptEdits] \
  [--model opus]
```

## Finding the script

```bash
# Check user-level install, then project-level, then local
SPAWN_LEAD="$(find ~/.claude/skills -path "*/scatter/bin/spawn-lead" -type f 2>/dev/null | head -1)"
SPAWN_LEAD="${SPAWN_LEAD:-$(find .claude/skills -path "*/scatter/bin/spawn-lead" -type f 2>/dev/null | head -1)}"
SPAWN_LEAD="${SPAWN_LEAD:-./bin/spawn-lead}"
```

## Flags

- `--name` (required): Short identifier for the team. Used as the tmux pane title.
- `--cwd` (required): Absolute path — repo for code tasks, vault path for research/writing tasks.
- `--prompt` (required): Full instructions for the team lead. Must be self-contained.
- `--output-path`: Directory where the team writes structured findings (e.g., vault's scatter-output/). Team lead gets instructions to write `<output-path>/<name>.md`.
- `--worktree`: Git branch name. Creates an isolated worktree at `<cwd>/.worktrees/<name>/`. **Use when multiple code teams target the same repo.**
- `--context`: Project name from station.toml. Injects vault briefing + CLAUDE.md + memory into the prompt.
- `--permission-mode`: Claude Code permission mode. Default: `acceptEdits`.
- `--model`: Model override (e.g., `opus`, `sonnet`).

## Layout

- First spawn: splits horizontally (Scatter command station left, team lead right)
- Subsequent spawns: stack vertically on the right side
- Focus stays on the command station pane

## Worktree isolation

When spawning multiple teams on the same repo, always use `--worktree` with unique branch names:

```bash
spawn-lead --name team-a --cwd /repo --worktree feat/team-a --prompt "..."
spawn-lead --name team-b --cwd /repo --worktree feat/team-b --prompt "..."
```

## Task types

- **Code tasks**: `--cwd` to repo, use `--worktree` for parallel work, `--output-path` for summary
- **Research tasks**: `--cwd` to vault path, `--output-path` for findings
- **Writing tasks**: `--cwd` to vault path, `--output-path` for drafts
- **GitHub tasks**: `--cwd` to repo, `--output-path` for action summary

## Prompt guidelines

The team lead is a fresh Claude Code session with ALL capabilities (web search, gh CLI, plugins). Include:
1. Role and team name
2. Task type (code, research, writing, github)
3. Project context
4. Clear objective
5. Key files and architecture notes
6. Instructions to create an Agent Team (TeamCreate + teammates) for complex tasks
7. Git branch info if using worktree
8. Definition of done
