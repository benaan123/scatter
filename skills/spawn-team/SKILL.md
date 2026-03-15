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
- `--cwd` (required): Absolute path to the project directory.
- `--prompt` (required): Full instructions for the team lead. Must be self-contained.
- `--worktree`: Git branch name. Creates an isolated worktree at `<cwd>/.worktrees/<name>/`. **Use when multiple teams target the same repo.**
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

## Prompt guidelines

The team lead is a fresh Claude session. Include:
1. Role and team name
2. Project context
3. Clear objective
4. Key files and architecture notes
5. Instructions to create an Agent Team (TeamCreate + teammates)
6. Git branch info if using worktree
7. Definition of done
