# Claude Harness

Quality enforcement harness for Claude CLI that implements Anthropic's long-running agent pattern with code quality gates.

## Features

- **Anti-slop enforcement** - Blocks greeting phrases like "I'd be happy to", "Let me", etc.
- **No-placeholder enforcement** - Blocks TODO, FIXME, pass, stub, unimplemented code
- **Harness compliance** - Validates session protocol (progress file, git history, features.json)
- **Anthropic session pattern** - Initializer vs continuing mode detection
- **Golden ticket bypass** - Skip enforcement for explain/help/emergency requests
- **Retry logic** - Automatic retries with prompt softening
- **Snapshot system** - Git-based rollback capability
- **CLAUDE.md auto-creation** - Generates project rules file

## Installation

```bash
# Backup original claude
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

# Force harness mode
claude --harness
```

## Session Modes

**Initializer Mode** (no claude-progress.txt exists):
- Creates claude-progress.txt, features.json, init.sh
- Lists all features needed
- Implements ONE feature

**Continuing Mode** (claude-progress.txt exists):
- Reads progress file and git history
- Picks ONE feature from features.json
- Updates progress when done

## Enforcement Rules

The harness enforces these quality gates:

1. No slop patterns (greeting phrases, filler)
2. No placeholders (TODO, FIXME, pass, stub)
3. Tests required with every change
4. Patch/edit mode for existing files
5. One feature at a time

## Golden Tickets

These patterns bypass enforcement:
- `explain ...`
- `what is ...`
- `how does ...`
- `EMERGENCY:` / `HOTFIX:`
- `status` / `help`

## License

MIT
