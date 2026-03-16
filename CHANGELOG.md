# Changelog

## 0.3.0 — Swarm Architecture Rewrite

### Changed
- **Orchestrator prompt slimmed from 530→219 lines** — removed 210 lines of prescriptive team archetype templates, team leads now decide their own internal structure
- **Added Batch Spawn Protocol** — orchestrator now parses all tasks first, then spawns all teams at once instead of sequential spawning
- **Active gather loop** — orchestrator proactively polls `station poll` after spawning instead of waiting for user to request findings
- **Task shape routing table** — replaced 6 verbose archetypes with compact routing table (code/fix/research/write/review/explore/github → cwd + worktree rules)
- **Simplified team lead prompts** — reduced from 15+ lines to 5-line template, no sub-team prescriptions

### Added
- `--shape` flag in `spawn-lead` for metadata tracking (recorded in ledger)
- `station poll` subcommand — status check + auto-gather for done teams in one command
- Batch spawn guidance in orchestrator — "parse all tasks first, spawn all at once"

### Improved
- Removed token budget spent on git/worktree prescriptions, focusing orchestrator on routing and gathering
- Made orchestrator "brain-light" — team leads are full Claude Code sessions with all capabilities

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
