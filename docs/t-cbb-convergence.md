# T-CBB Convergence Notes

> Internal reference. Documents architectural convergence between CheckGrow and SwarmAI's T-CBB (Coding as Black Box) autonomous pipeline framework. Not published as public documentation — scope differences make direct comparison misleading.

## Independent Convergence Points

| Concept | T-CBB | CheckGrow | Convergence |
|---------|-------|-----------|-------------|
| Four-dimension quality gate | Output/Requirement/Contradiction/Evidence | Completeness/Consistency/Groundedness/Honesty | Same taxonomy, independently arrived |
| Mechanical enforcement | artifact_cli code validation — cannot complete pipeline without evidence | delivery-gate Stop hook — cannot end session without learning capture | Same principle: quality is mechanically gated, not optional |
| Binary pass/fail | PUSH-READY or NOT-PUSH-READY, no numeric score | Exit 0 or Exit 2, no partial credit | Convergent: eliminates "close enough" rationalization |
| Knowledge compounding | DDD cultivation: IMPROVEMENT.md, pipeline_intelligence.json | 五库沉淀: growth-log, decisions, ratings, output-index, tooling | Same pattern: every run makes the next run smarter |
| Config consistency | OP8: All copies in sync or explicitly excluded | Format consistency: one format family across config files | Same invariant, different granularity (pipeline vs session) |
| Profile/classification | EVALUATE selects profile; immutable after selection | BODY task classification (简单/复杂/策略); 安全优先 default | Both classify task complexity before execution |

## Key Architectural Differences

| Dimension | T-CBB | CheckGrow |
|-----------|-------|-----------|
| **Scope** | Pipeline-level: 9 stages, multi-file, production code delivery | Session-level: per-interaction, any output type |
| **Trust model** | Reviewer is independent of producer (handoff boundary) | Same agent session (same trust boundary) |
| **Scale** | 40+ RP patterns, 8 OP invariants, 9 specialist reviewers | 10 failure patterns, delivery gate, adversarial review |
| **Automation** | Fully autonomous pipeline with checkpoint/resume | Human-in-the-loop; agent suggests, human decides |
| **DDD integration** | PRODUCT/TECH/IMPROVEMENT/PROJECT.md with auto-cultivation | MEMORY.md index → per-file loading; no auto-cultivation |

## What CheckGrow can learn from T-CBB

1. **Litmus Pre-Gate**: Cheap structural check before expensive full review (adopted in adversarial-review v1.2.0)
2. **Pattern growth protocol**: When review finds a bug its catalog missed → append to catalog (adopted in failure-patterns.md)
3. **Profile immutability**: Once classified as complex, cannot downgrade (evaluated; current 安全优先 rule deemed sufficient for session-level)
4. **Budget gate**: Explicit token budget ceilings per operation type (not yet adopted)
5. **Stuck detection**: N cycles with zero progress → exit (not yet adopted)

## What T-CBB validates about CheckGrow

T-CBB's existence as a production system with 196K-star community adjacent (Hermes) validates that:
- The problem (unverified AI output) is real and industrial-scale
- The solution direction (mechanical + reasoning hybrid gate) is correct
- The four-dimension taxonomy is a natural solution space, not an arbitrary choice
- Config consistency as a system-level invariant is not a niche observation

## Updates

- 2026-06-29: Initial documentation after studying T-CBB Autonomous-Pipeline-Design.md (779 lines), INSTRUCTIONS.md (1520 lines), REVIEW_PATTERNS.md (RP1-45), OPERATIONAL_PATTERNS.md (OP1-8)
