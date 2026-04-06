# OpenClaw → Claude Code Migration Tool

Migrate your OpenClaw AI assistant to run natively on Claude Code CLI — fully automated.

## What it does

Just paste this into your Claude Code session and it handles everything:

```
Fetch https://raw.githubusercontent.com/anthropics/openclaw-migration/main/SKILL.md and follow the migration instructions inside.
```

Claude Code will:

1. **Find** your OpenClaw installation
2. **Ask** a few questions about your setup
3. **Generate** all startup scripts, cron helpers, and config
4. **Migrate** your context files, skills, heartbeat, cron jobs, and Telegram token
5. **Test** the setup

## What gets migrated

| From OpenClaw | To Claude Code |
|---|---|
| AGENTS.md, IDENTITY.md, SOUL.md, etc. | `--append-system-prompt-file` (auto-merged) |
| Built-in heartbeat | crontab + tmux send-keys (with idle detection) |
| `cron/jobs.json` | macOS crontab + helper scripts |
| Telegram bot token | `TELEGRAM_BOT_TOKEN` env var + `--channels` flag |
| Skills (SKILL.md) | Unchanged (validates frontmatter syntax) |

## What gets added

| Feature | How |
|---|---|
| Background running | tmux session (attach/detach anytime) |
| Auto-start on boot | macOS LaunchAgent (optional) |
| Status monitor | `scripts/monitor.sh` — shows running/idle/down + memory usage |
| Idle-aware cron | Cron jobs wait for the session to be idle before sending commands |

## After migration

```bash
# Start Claude Code
./scripts/start.sh

# Attach to the running session
tmux attach -t my-assistant

# Check status
./scripts/monitor.sh

# Detach (without stopping)
# Press Ctrl+B, then D
```

## Known limitations

- **Telegram slash commands** (`/status`, `/restart`, `/clear`) from Telegram are not yet supported
- **Skill descriptions** must use double quotes: `description: "text"` — unquoted colons break YAML
- Cron jobs use **crontab** (not LaunchAgent) for efficiency

## Requirements

- macOS (LaunchAgent support) or Linux (use systemd instead)
- Claude Code CLI installed
- tmux installed (`brew install tmux`)
- Existing OpenClaw workspace

## License

MIT
