![License](https://img.shields.io/badge/license-MIT-blue)
![Claude Code](https://img.shields.io/badge/Claude_Code-skills-blueviolet)

# Scatter

> Scatter your thoughts. Gather the results.

An "always on" multi-agent command station for Claude Code. You talk to Scatter in tmux — it spawns team leads in split panes, each a full Claude Code session with its own agent team. If two teams work on the same repo, they each get a git worktree so they don't step on each other.

```
┌─────────────────────┬───────────────────────┐
│                     │  [auth-team]           │
│   Scatter           │  Refactoring auth...   │
│   (you talk here)   ├───────────────────────┤
│                     │  [api-team]            │
│                     │  Adding endpoints...   │
└─────────────────────┴───────────────────────┘
```

---

## Install

Requirements: [Claude Code](https://claude.ai/code), [tmux](https://github.com/tmux/tmux) (>= 3.0), Git. Works on macOS and Linux (on Windows, use WSL).

Open Claude Code and paste this. Claude will do the rest.

```
Install scatter:
run git clone https://github.com/benaan123/scatter.git ~/.claude/skills/scatter && cd ~/.claude/skills/scatter && ./setup
then add a "scatter" section to CLAUDE.md with exactly these descriptions:
- /scatter — Launch the command station (opens in tmux)
- /spawn-team — Spawn a team lead in a tmux pane for parallel work
- /station — Monitor and manage running teams (list, capture, kill, merge)
- `scatter` shell command — launches the command station from terminal
```

### What gets installed

- Skill files (Markdown prompts) in `~/.claude/skills/scatter/`
- Symlinks at `~/.claude/skills/scatter-launch`, `~/.claude/skills/spawn-team`, `~/.claude/skills/station` pointing into the scatter directory
- Shell aliases: `scatter` (launches tmux command station) and `station` (team management CLI)
- Everything lives inside `.claude/`. Nothing touches your PATH beyond the shell alias block.

---

## Usage

From your terminal:

```bash
scatter
```

Or from inside a Claude Code session (if you're in tmux):

```
/scatter
```

This opens the command station in a new tmux window. Your current session stays running — switch between them with `Ctrl-b n` / `Ctrl-b p`.

Then just talk to it:

> "Spin up a team to refactor auth in ~/code/myapp and another for the API"

> "I need two teams on the same repo — one for backend, one for frontend"

> "How are my teams doing?"

---

## Station commands

```bash
station list              # Active team panes
station capture auth-team # Read a team's terminal output
station capture-all       # Read all teams
station kill auth-team    # Kill a team
station cleanup auth-team # Kill + remove git worktree
station merge auth-team   # Merge team's branch into main
station layout            # Re-tile panes
station resume            # Replay session ledger
```

---

## Vault integration (optional)

If you use Obsidian, Scatter can pull context from per-project vaults — briefings, journal entries, search. Copy `station.example.toml` to `station.toml` and configure your projects. Works fine without it.

---

## Uninstall

```bash
cd ~/.claude/skills/scatter && ./setup --undo && rm -rf ~/.claude/skills/scatter
```

---

## License

[MIT](LICENSE)
