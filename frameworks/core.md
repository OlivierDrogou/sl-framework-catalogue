# CORE

**Repo:** [github.com/DariuszNewecki/CORE](https://github.com/DariuszNewecki/CORE)
**Language:** Python
**License:** MIT
**Claim:** Governance runtime — executable constitutional governance for AI coding agents

## Grid

| LLM→Tool direct? | Explicit boundary? | Boundary nature | Verification | On violation | L→S contamination open? |
|:-:|:-:|:-:|:-:|:-:|:-:|
| No | Yes (named) | Mixed (6+1) | Runtime | Halt | Partially |

## Evidence

### LLM→Tool direct? → No

CORE interposes 7 verification engines (gates) between Will (LLM deliberation) and Body (execution). Every operation follows a sequenced pipeline:
```
INTERPRET → PLAN → GENERATE → VALIDATE → STYLE CHECK → EXECUTE
```
Execution arrives only after four verification steps. README: "If a blocking rule fails, execution halts. No partial states."

### Explicit boundary? → Yes (named)

The Mind/Will/Body separation is documented, named, and structuring. 92 constitutional rules in YAML (`.intent/`), 7 enforcement engines in `src/mind/`, pipeline sequenced by phases.

### Boundary nature → Mixed (6 deterministic + 1 LLM)

The 7 gates:
- `ast_gate` — structural code analysis (deterministic)
- `glob_gate` — file path enforcement (deterministic)
- `intent_gate` — write authorization (deterministic)
- `knowledge_gate` — responsibility and ownership (deterministic)
- `workflow_gate` — phase sequencing (deterministic)
- `regex_gate` — text patterns (deterministic)
- `llm_gate` — semantic verification (probabilistic)

The 6:1 ratio is a deliberate design decision. Dariusz Newecki: "Deterministic when possible. LLM only when necessary."

The `llm_gate` remains a liquid component inside the boundary — the residual contamination point, attenuated by 6 deterministic gates upstream but structurally present.

### Verification → Runtime

Gates evaluate at runtime, not compile-time. Python doesn't structurally prevent a developer from importing Body directly from Will, bypassing Mind. The discipline is in the repo and constitution — not in the compiler.

### On violation → Halt

Three enforcement levels declared in each YAML rule:
- **Blocking** — total halt, no changes applied
- **Reporting** — logged, continues
- **Advisory** — suggestion

Default for constitutional rules is blocking (fail-closed). Audit trail is complete — every decision is traced.

```yaml
- id: "autonomy.lanes.boundary_enforcement"
  statement: "Autonomous agents MUST NOT modify files
              outside their assigned lane"
  enforcement: "blocking"
  authority: "constitution"
  phase: "runtime"
```

### L→S contamination open? → Partially

The `llm_gate` is a liquid path inside the boundary (liquid validating liquid). Risk attenuated by 6 deterministic gates upstream. Python convention doesn't prevent bypass at source code level. CORE doesn't explicitly document its residual contamination vectors as structural risk (unlike Drogou 2026, section 5.6).

## Positioning

CORE is the first framework in this catalogue that breaks the pattern on every column. It has the boundary, it's named, it's mostly deterministic, it halts on violation. Two limits remain compared to Valve: the `llm_gate` introduces a probabilistic component in the boundary, and the separation is conventional (Python directories) rather than structural (compile-time type system).

Dariusz Newecki, in response to the paper: **"Enforcement didn't kill capability — it channeled it."** This is the empirical confirmation of the paper's thesis: the valve doesn't constrain the LLM. It makes its output usable.
