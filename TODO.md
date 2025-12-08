# Claude Code Harness - TODO

Status: Active Development
Last Updated: 2025-12-08

---

## Priority 1: Profiles System

### [ ] Implement --profile Flag
- Load profile-specific: system prompts, output schemas, allowed tools, guardrails
- Profile config in JSON/YAML
- Profile inheritance support

### [ ] Built-in Profiles
- [ ] `code_edit` - Standard code editing with tests
- [ ] `re_decompile` - RE mode for decompilation tasks
  - Input: disassembly text, arch description, hints
  - Output: single C function, no commentary
  - Built-in check: `clang -fsyntax-only` validation
  - Auto-regenerate on compile failure (1-2 retries)
- [ ] `sandbox_exploit` - Exploit/security sandbox mode
  - Tools-only interaction (no raw shell)
  - Time/step limits
  - Impact reporting ($ stolen in sim, pwned status)
- [ ] `doc_summarize` - Documentation/legal summary mode

---

## Priority 2: Tool Layer Abstraction

### [ ] Define Tool Interface
- Harness doesn't know "docker" or "curl"
- Abstract tools:
  - `run_tests()`
  - `build_project(name)`
  - `execute_scenario()`
  - `query_disassembly(ea)`
- Tools implemented via config file + Python/Go layer

### [ ] Tool Config Format
```json
{
  "tools": {
    "run_tests": {
      "command": "pytest",
      "timeout": 60,
      "working_dir": "."
    }
  }
}
```

---

## Priority 3: Verifier Hooks

### [ ] Post-Run Verification System
- After each run, call verifier hooks
- If verifier fails, flag run as "NOT OK" even if model said "done"

### [ ] Built-in Verifiers
- [ ] `verify_code_compiles` - Check code compiles
- [ ] `verify_tests_pass` - Check tests pass
- [ ] `verify_sandbox_score` - Check sandbox score threshold
- [ ] `verify_syntax` - Language-specific syntax check

---

## Priority 4: Logging & Replay

### [ ] Strict Logging
Every run logs to single JSON file:
- Inputs (prompt, profile, config)
- Model messages
- Tool calls and results
- Verifier results
- Timestamps

### [ ] Replay Capability
- Replay any session from log
- Debug failed runs
- Audit trail for all actions

---

## Priority 5: Safety & Hardening

### [ ] Loop/Retry Control
- Track: same error occurring N times
- Track: same tool called with same args repeatedly
- Hard-stop and surface: "Agent is stuck in a loop, exiting."

### [ ] Context Discipline
- Refuse prompts over configured size (unless profile allows)
- For big tasks: require plan step, then sub-steps with narrow contexts
- Each step validated independently

### [ ] Confirmation Bias Counter
For security/refactor profiles:
- [ ] Explicit "counter-argument" phase
- [ ] Verification step: model must try to prove itself wrong

---

## References

Based on:
- VentureBeat "AI coding agents aren't production-ready" failure modes
- InterviewReady agent architecture patterns
- SCONE-bench sandbox methodology
