![License](https://img.shields.io/badge/license-MIT-blue)
![Claude Code](https://img.shields.io/badge/Claude_Code-skills-blueviolet)

# Scatter

> Scatter your thoughts. Gather the results.

I couldn't find a Claude Code plugin focused on spawning teams on rapid-fire ideas across projects, for both coding and non-coding tasks, so I built Scatter.

It's an "always on" multi-agent command station that runs in tmux. You talk to it, it spawns team leads in split panes — each a full Claude Code session with its own context window. If two teams work on the same repo, they each get a git worktree so they don't step on each other.

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

```bash
git clone https://github.com/benaan123/scatter.git ~/.claude/skills/scatter
cd ~/.claude/skills/scatter && ./setup
```

### What gets installed

- Skill files (Markdown prompts) in `~/.claude/skills/scatter/`
- Symlinks at `~/.claude/skills/spawn-team`, `~/.claude/skills/station` pointing into the scatter directory
- Shell aliases: `scatter` (launches tmux command station) and `station` (team management CLI)
- Everything lives inside `.claude/`. Nothing touches your PATH beyond the shell alias block.

---

## Usage

Open a new terminal tab and run:

```bash
scatter
```

This opens the command station in tmux. Switch between windows with `Ctrl-b n` / `Ctrl-b p`.

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
