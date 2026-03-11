# CrewAI

**Repo:** [github.com/crewAIInc/crewAI](https://github.com/crewAIInc/crewAI)
**Language:** Python
**License:** MIT
**Claim:** Multi-agent collaboration — specialized agent "crews"

## Grid

| LLM→Tool direct? | Explicit boundary? | Boundary nature | Verification | On violation | L→S contamination open? |
|:-:|:-:|:-:|:-:|:-:|:-:|
| Yes | Implicit | Absent | Never | Silent | Yes |

## Evidence

### LLM→Tool direct? → Yes

`CrewAgentExecutor` parses LLM response and calls `execute_tool_and_check_finality()`. The LLM selects the tool by name, generates arguments, and `tool._run(args)` is called directly. Official docs confirm: the LLM selects tools based on textual description.

### Explicit boundary? → Implicit

CrewAI distinguishes Crews (autonomous agents) and Flows (deterministic, event-driven workflows). This sketches a conceptual boundary, but it doesn't apply at the tool boundary: when an agent calls a tool, there's no checkpoint.

Optional Pydantic `args_schema` on custom tools — but it's the developer's responsibility, not a framework property.

### Verification → Never

Documentation recommends adding "guardrails around tool usage and outputs" for production but provides no built-in mechanism to do so.

### On violation → Silent

Official docs recommend: "Return error messages as strings rather than raising exceptions, allowing the agent to continue with error feedback." If the tool fails, the error goes back to the LLM as text. Liquid handles solid's error.

### L→S contamination open? → Yes

**Bug #3154** ([github.com/crewAIInc/crewAI/issues/3154](https://github.com/crewAIInc/crewAI/issues/3154)): the agent doesn't actually invoke tools — it simulates execution and fabricates results. The LLM produces a "Thought → Action → Observation → Final Answer" that looks correct, but the tool was never called. Complete contamination: liquid hallucinating the entirety of solid's result.

## Positioning

CrewAI is slightly more structured than LangChain — the Crews/Flows separation shows architectural intent, and Pydantic `args_schema` is an embryonic contract. But on the column that matters (contamination open?), the result is the same. And bug #3154 illustrates a failure mode LangChain doesn't document: contamination where the tool isn't even executed but the system believes it was.
