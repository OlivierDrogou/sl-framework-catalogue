# AutoGen / AG2

**Repo:** [github.com/microsoft/autogen](https://github.com/microsoft/autogen) + [github.com/ag2ai/ag2](https://github.com/ag2ai/ag2)
**Language:** Python
**License:** MIT (AutoGen), Apache 2.0 (AG2)
**Claim:** Multi-agent conversation — agents communicating via messages

## Grid

| LLM→Tool direct? | Explicit boundary? | Boundary nature | Verification | On violation | L→S contamination open? |
|:-:|:-:|:-:|:-:|:-:|:-:|
| Yes | Implicit | Absent | Never | Silent | Yes |

## Evidence

### LLM→Tool direct? → Yes

**v0.2** had a caller/executor separation:
```python
register_function(
    get_weekday,
    caller=date_agent,       # recommends
    executor=executor_agent,  # executes
)
```
But `executor_agent` executes automatically without precondition verification (`human_input_mode="NEVER"`). The separation is organizational, not contractual.

**v0.4+**: `AssistantAgent` merges calling and execution:
```
ToolCallRequestEvent → ToolCallExecutionEvent → ToolCallSummaryMessage
```
Between request and execution — nothing.

### Explicit boundary? → Implicit

The caller/executor split in v0.2 sketched a boundary — the component that recommends isn't the one that executes. But the executor is a blind agent that executes everything asked, without verification. v0.4 merged both into a single `AssistantAgent`, abandoning even this sketch.

### Verification → Never

Except `human_input_mode=ALWAYS`, which isn't deterministic (humans are fallible) and isn't the norm in production.

### On violation → Silent

LLM hallucinates a function name → error raised. No structured refusal, no halt, no fallback.

`CodeExecutorAgent` executes arbitrary LLM-generated code (`LocalCommandLineCodeExecutor`). Containment: Docker. Which isolates the environment but doesn't verify code content.

### L→S contamination open? → Yes

Direct path in both versions. `CodeExecutorAgent` amplifies the risk: the LLM writes arbitrary Python, the system executes it. This is the exact equivalent of the uncontrolled bash tool documented as an open attack surface in Drogou (2026), section 5.6.

## Positioning

AutoGen is the most instructive case study. It had the correct architectural intuition in v0.2 (separate who recommends from who executes), then sacrificed it in v0.4 to simplify the API. Productivity 1, integrity 0. This illustrates the paper's argument: without explicit formalization (S/L separation), even a good intuition dissolves under the pressure of simplicity.

**Note:** AutoGen and Semantic Kernel are merging into the "Microsoft Agent Framework." The outcome of this merger will determine whether Microsoft's agent architecture retains the filter mechanisms from SK or loses them as it lost the caller/executor split from AutoGen.
