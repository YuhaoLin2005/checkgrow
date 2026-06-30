# Hybrid Gate Architecture

> Mechanical verification + reasoning audit. Don't trust agent claims — verify.

## The Two Halves

| Layer | Component | What it checks | How |
|---|---|---|---|
| **Mechanical** | delivery-gate (Stop hook) | File timestamps, disk space, rationalization patterns | Python script, regex matching, `os.path.exists()`, `os.stat()` |
| **Mechanical** | self-audit Step 0 | Claimed output files exist and are readable | `os.path.exists()` before any reasoning |
| **Reasoning** | self-audit Steps 1-4 | Completeness, Consistency, Groundedness, Honesty | Agent asks four questions, cites evidence |
| **Reasoning** | adversarial-review | Independent subagent finds bugs | `Agent(subagent_type="code-reviewer")` with adversarial prompt |
| **Reasoning** | dual-pool-review | Multi-persona cross-validation | Fixed pool (known experts) + random pool (web-searched) |

## Why Hybrid

Pure reasoning audits (self-audit v1.0) have a blind spot: **they depend on the agent being honest.** If the agent claims "I generated report.md" but the file doesn't exist, the reasoning audit can still pass — the agent answers "yes, I did that" to the Completeness question.

Pure mechanical checks (delivery-gate) have a different blind spot: **they can't assess reasoning quality.** The file exists, the growth-log was updated — but is the content correct? Is there a contradiction? Mechanical checks can't answer these.

**Hybrid = mechanical catches lies of omission, reasoning catches errors of thinking.**

### The Mechanical Layer is Itself Dual-Layer

As of [delivery-gate](https://github.com/gategrow/delivery-gate) v2.0, the mechanical gate splits into two sub-layers:

| Sub-layer | Component | Principle | Blocks? |
|-----------|-----------|-----------|---------|
| **Process (soft)** | config-health.py | Monitors rule execution via `[✓MARKER]` counting | Never (exit 0 always) |
| **Output (hard)** | quality-gate.py | Enforces five-library updates via mtime | ≥3 stale → block |

The boundary isn't "importance" — it's **"can this be fixed later?"** Missed rule execution can be retroactively marked and counted. Missed output records are lost forever.

## The T-CBB Connection

SwarmAI's T-CBB (Coding as Black Box) deploys the same pattern at pipeline handoff boundaries:

| T-CBB Dimension | CheckGrow Equivalent |
|---|---|
| Output-artifact present | self-audit Step 0 + delivery-gate file checks |
| Requirement coverage | self-audit Completeness (v2.0: requirement→output mapping) |
| Contradiction scan | self-audit Consistency |
| Evidence audit | self-audit Groundedness |

Convergent evolution: two independent teams arrived at the same four-dimension quality gate. The pattern is real.

## Execution Order

```
Agent claims task complete
        │
        ▼
[Mechanical: Step 0]
  └─ Claimed files exist? ── NO → BLOCK, report to user
        │ YES
        ▼
[Reasoning: Four Questions]
  └─ C/C/G/H audit
        │
        ▼
[Optional: Adversarial Review]
  └─ Spawn subagent(s) for code/docs
        │
        ▼
[Mechanical: delivery-gate (dual-layer)]
  ├─ config-health.py: rule markers? ── monitor, never blocks
  └─ quality-gate.py: 5-lib mtime? Disk? ── NO → BLOCK
        │ YES
        ▼
     DELIVER
```

## When to add more mechanical checks

Ask: "Is there a way the agent could claim success while the artifact is wrong?" If yes → add a mechanical check.

Examples:
- Agent claims "ran all tests" → mechanical: check if test output file was modified this session
- Agent claims "no secrets exposed" → mechanical: `grep` for API key patterns in output
- Agent claims "all requirements covered" → mechanical: diff input spec keywords against output text
