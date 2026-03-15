# Changelog

## 0.2.0 — Rename to Scatter

- Rebranded from claude-team-orchestrator to **Scatter**
- Tagline: "Scatter your thoughts. Gather the results."
- Updated all user-facing references, docs, and plugin metadata
- Ledger directory moved from `~/.local/share/cstation/` to `~/.local/share/scatter/`
- Status files moved from `/tmp/cstation-status/` to `/tmp/scatter-status/`

## 0.1.0 — Initial release

- Orchestrator agent that spawns and manages parallel Claude Code team leads
- `bin/spawn-lead` for creating team lead sessions in tmux panes
- `bin/station` for monitoring, capturing output, and cleaning up team panes
- Git worktree isolation for multiple teams on the same repository
- `scatter` launcher via `bin/setup`
