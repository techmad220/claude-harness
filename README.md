# Claude Harness

Quality enforcement harness for Claude CLI that implements code quality gates, session tracking, and safety features.

## Features

- **Anti-slop enforcement** - Blocks greeting phrases like "I'd be happy to", "Let me", etc.
- **No-placeholder enforcement** - Blocks TODO, FIXME, pass, stub, unimplemented code
- **Security checks** - OWASP top 10 vulnerability detection
- **Snapshot & Rollback** - Automatic git snapshots before changes with easy rollback
- **Session tracking** - Anthropic's long-running agent pattern (initializer/continuing modes)
- **Auto mode** - Automatically continue sessions until project complete
- **Metrics tracking** - Track violations and sessions over time
- **Context management** - Save/load project context between sessions
- **Quality hints** - Non-blocking warnings for complexity, test coverage, performance

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

# Auto-continue until all features complete
claude "build a REST API" --auto

# Bypass harness, run Claude directly
claude --raw [args]

# Rollback to previous snapshot
claude --rollback

# Show session metrics
claude --metrics

# Load saved project context
claude --context
```

## Flags

| Flag | Description |
|------|-------------|
| `--raw` | Bypass all harness logic |
| `--harness` | Force harness mode |
| `--auto` | Auto-continue sessions until project complete |
| `--rollback` | Restore previous snapshot |
| `--metrics` | Show session metrics and violation history |
| `--context` | Load saved project context |
| `-h, --help` | Show help |

## Enforcement Rules

| Check | What it catches |
|-------|-----------------|
| No slop | Greeting phrases, filler words |
| No placeholders | TODO, FIXME, pass, stub |
| Security | SQL injection, XSS, command injection, hardcoded secrets, etc. |
| Harness compliance | Session protocol violations |

## Quality Hints (Non-blocking)

- Code complexity warnings (deep nesting, long functions)
- Test coverage reminders
- Performance anti-pattern detection

## E2E Verification (Anthropic-style)

The harness now implements Anthropic's recommended verification pattern:

- **200+ feature granularity** - Initializer creates comprehensive feature list
- **Puppeteer MCP integration** - For web apps, test as a real user would
- **Verify before marking passing** - Features must pass E2E verification
- **No premature completion** - Can't mark features done without evidence

### Feature List Format

```json
{
  "features": [
    {
      "id": "feature-001",
      "category": "functional|ui|error-handling|edge-case",
      "description": "Specific testable behavior",
      "verification": "How to verify this works",
      "passes": false
    }
  ]
}
```

### Verification Methods

| Project Type | Verification Method |
|--------------|---------------------|
| Web apps | Puppeteer MCP - navigate, interact, screenshot |
| CLI/API | Run commands, verify output |
| Libraries | Integration tests |

## Session Modes

**Initializer Mode** (new project):
- Creates claude-progress.txt, features.json, init.sh
- Lists all features needed
- Implements ONE feature per session

**Continuing Mode** (existing project):
- Reads progress file and git history
- Picks ONE feature from features.json
- Updates progress when done

## Auto Mode

Use `--auto` to automatically continue sessions until all features are complete:

```bash
claude "build a user management system" --auto
```

The harness will:
1. Run initializer session to create features.json
2. Check if all features have `"passes": true`
3. If not complete, automatically start next session
4. Repeat until all features done
5. Show celebration message when complete

## Security Checks

- SQL Injection
- XSS (Cross-Site Scripting)
- Command Injection
- Path Traversal / LFI / RFI
- Hardcoded Secrets
- SSRF (Server-Side Request Forgery)
- Insecure Deserialization
- XXE (XML External Entity)
- Dangerous Commands
- Weak Cryptography

All security checks include false-positive detection to avoid flagging safe code patterns.

## License

MIT
