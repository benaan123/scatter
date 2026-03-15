---
name: scatter
version: 0.2.0
description: "Launch the Scatter command station. Use when the user says 'scatter', 'launch scatter', 'start the command station', 'open scatter', or wants to start multi-agent orchestration."
---

# Launch Scatter Command Station

Run this command to launch Scatter. It handles all cases: in tmux, not in tmux, session already running.

```bash
if [ -n "$TMUX" ]; then
  tmux new-window -n commander "claude --agent scatter"
elif tmux has-session -t command-station 2>/dev/null; then
  tmux new-window -t command-station -n commander "claude --agent scatter"
  echo "ATTACH"
else
  tmux new-session -d -s command-station -n commander "claude --agent scatter"
  echo "ATTACH"
fi
```

## After launching

**If in tmux:** tell the user Scatter is running in a new tmux window. Switch with `Ctrl-b n` / `Ctrl-b w`. Come back with `Ctrl-b p`.

**If output says ATTACH:** tell the user to run `tmux attach -t command-station` in their terminal.
