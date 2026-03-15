---
name: station
version: 0.2.0
description: Monitor and manage active team lead tmux panes. Use to check status of running teams, capture their screen output, kill finished teams, or clean up worktrees.
---

# Station — Team Lead Monitor

Monitor and manage Claude Code team lead panes in the Scatter command station tmux session.

## Usage

```bash
<plugin-bin>/station <command> [args]
```

## Finding the script

```bash
# Check user-level install, then project-level, then local
STATION="$(find ~/.claude/skills -path "*/scatter/bin/station" -type f 2>/dev/null | head -1)"
STATION="${STATION:-$(find .claude/skills -path "*/scatter/bin/station" -type f 2>/dev/null | head -1)}"
STATION="${STATION:-./bin/station}"
```

## Commands

### `list` — Show all panes
```bash
station list
```
Shows pane index, title (team name), PID, running command, size, and working directory.

### `capture <name>` — Read a team lead's screen
```bash
station capture auth-refactor
```
Captures the visible terminal output of the named team lead's pane. Use this to check what a team lead is currently doing.

### `capture-all` — Read all team lead screens
```bash
station capture-all
```
Captures every team lead pane (skips pane 0 / command station). Best way to get a full status overview.

### `kill <name>` — Kill a team lead pane
```bash
station kill auth-refactor
```
Terminates the team lead's Claude session and closes the pane.

### `cleanup <name>` — Kill pane + remove worktree
```bash
station cleanup auth-refactor
```
Kills the pane AND removes the git worktree if the team was using one. Use after a team's branch has been merged.

### `worktrees <path>` — List worktrees for a repo
```bash
station worktrees /path/to/repo
```
Shows all git worktrees, useful to see which teams have active branches.

### `layout` — Re-tile panes
```bash
station layout
```
Re-tiles all panes evenly. Useful after killing a team lead.

### `resume` — Session resume
```bash
station resume
```
Reads the durable session ledger (`~/.local/share/scatter/ledger.jsonl`) and shows what happened since last session: running teams, completed work, crashed panes, and branches ready to merge. This is the first thing the Scatter orchestrator runs on startup.

### `merge <name>` — Merge a team's worktree branch
```bash
station merge auth-refactor
```
Merges the team's feature branch into main, removes the worktree, and deletes the branch. Refuses to proceed if there are uncommitted changes or merge conflicts. Records the merge in the session ledger.

## Status check workflow

When asked "how are my teams doing?":
1. Run `station resume` for the big picture
2. Run `station capture-all` for detailed progress
3. For each team's output, identify:
   - Current phase (planning, implementing, testing, done)
   - Any permission prompts waiting
   - Task progress
4. Summarize concisely
