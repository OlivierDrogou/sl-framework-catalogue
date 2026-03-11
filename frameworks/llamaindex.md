# LlamaIndex

**Repo:** [github.com/run-llama/llama_index](https://github.com/run-llama/llama_index)
**Language:** Python + TypeScript
**License:** MIT
**Claim:** RAG + agents — powerful abstractions for search, indexing, and agents

## Grid

| LLM→Tool direct? | Explicit boundary? | Boundary nature | Verification | On violation | L→S contamination open? |
|:-:|:-:|:-:|:-:|:-:|:-:|
| Yes | Absent | — | Never | Silent | Yes |

## Evidence

### LLM→Tool direct? → Yes

`AgentWorkflow` and its agents (`FunctionAgent`, `ReActAgent`) follow:
```
LLM → ToolCall event → call_tool() → tool.execute() → ToolCallResult
```
The `call_tool` method (in `multi_agent_workflow.py`) "finds and executes the specific tool's code, writing results into ToolCallResult." The LLM produces a function call, the system resolves and executes it directly.

Official docs: "A tool can be called to perform custom actions, or retrieve extra information based on the LLM-generated input."

### Explicit boundary? → Absent

No component named as a boundary. LlamaIndex has three abstraction levels (AgentWorkflow → FunctionAgent/ReActAgent → Workflows), but none acts as a checkpoint between LLM recommendation and tool execution.

`return_direct` option on tools bypasses the LLM after execution (result goes directly to user), but this is a performance optimization, not a security verification.

### Verification → Never

Tool schemas are inferred automatically from Python signatures and docstrings. This is form validation (types), never business rules. `FunctionTool` accepts any Python function without constraint.

### On violation → Silent

No built-in structured refusal mechanism. If the tool fails, the error surfaces in `ToolCallResult` — no halt, no structured fallback.

### L→S contamination open? → Yes

Direct path, including in multi-agent mode. Agent handoff is an LLM decision (liquid), not a deterministic verification. Documentation mentions you can "insert whatever custom logic, caching or human-in-the-loop checks you require" in a custom Workflow — but it's the developer's responsibility to build the valve. The framework doesn't provide one.

## Positioning

LlamaIndex is the most modular of the Tier 1 frameworks. Its Workflows theoretically allow building a valve — the documentation opens the door. But this is exactly the problem identified in the paper: leaving the responsibility to the developer means in practice it's ignored. The framework provides flexibility, not the boundary.
