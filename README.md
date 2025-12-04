# Claude Harness

Quality enforcement harness for Claude CLI that implements code quality gates, session tracking, and safety features.

## Features

- **Anti-slop enforcement** - Blocks greeting phrases like "I'd be happy to", "Let me", etc.
- **No-placeholder enforcement** - Blocks TODO, FIXME, pass, stub, unimplemented code
- **Security checks** - Detects hardcoded secrets and dangerous commands
- **Snapshot & Rollback** - Automatic git snapshots before changes with easy rollback
- **Session tracking** - Anthropic's long-running agent pattern (initializer/continuing modes)
- **Golden ticket bypass** - Skip enforcement for explain/help requests

## Installation

```bash
# Backup original claude binary
mv ~/.local/bin/claude ~/.local/bin/claude.real

# Install harness
cp claude ~/.local/bin/claude
chmod +x ~/.local/bin/claude
```

## Usage

```bash
# Interactive mode (creates CLAUDE.md if needed)
claude

# Full harness mode with enforcement
claude "implement authentication"

# Bypass harness, run Claude directly
claude --raw [args]

# Rollback to previous snapshot if something goes wrong
claude --rollback
```

## Safety Features

### Snapshots
Before any changes, the harness automatically saves:
- Current git commit
- Uncommitted changes (diff)
- File hashes

If something goes wrong, run `claude --rollback` to restore.

### Security Checks
The harness scans output for:
- Hardcoded passwords, API keys, tokens, secrets
- Dangerous commands (rm -rf /, chmod 777, eval, etc.)

## Session Modes

**Initializer Mode** (new project):
- Creates claude-progress.txt, features.json, init.sh
- Lists all features needed
- Implements ONE feature per session

**Continuing Mode** (existing project):
- Reads progress file and git history
- Picks ONE feature from features.json
- Updates progress when done

## Enforcement Rules

| Check | What it catches |
|-------|-----------------|
| No slop | Greeting phrases, filler words |
| No placeholders | TODO, FIXME, pass, stub |
| Harness compliance | Session protocol violations |
| Security | Hardcoded secrets, dangerous commands |

## Golden Tickets

These patterns skip enforcement (for help/info requests):
- `explain ...`
- `what is ...`
- `how does ...`
- `help`
- `status`

## License

MIT
