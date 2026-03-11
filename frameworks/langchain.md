# LangChain / LangGraph

**Repo:** [github.com/langchain-ai/langchain](https://github.com/langchain-ai/langchain)
**Language:** Python
**License:** MIT
**Claim:** Developer productivity — connect an LLM to tools in a few lines

## Grid

| LLM→Tool direct? | Explicit boundary? | Boundary nature | Verification | On violation | L→S contamination open? |
|:-:|:-:|:-:|:-:|:-:|:-:|
| Yes | Absent | — | Never | Silent | Yes |

## Evidence

### LLM→Tool direct? → Yes

The `tool_node` from official LangGraph documentation:

```python
def tool_node(state: dict):
    result = []
    for tool_call in state["messages"][-1].tool_calls:
        tool = tools_by_name[tool_call["name"]]
        observation = tool.invoke(tool_call["args"])
        result.append(ToolMessage(content=observation, tool_call_id=tool_call["id"]))
    return {"messages": result}
```

`tool_call["args"]` passes directly to `tool.invoke()`. No checkpoint.

Routing (`should_continue`) checks only: did the LLM request a tool? If yes → execute. Not "does it have permission?" Not "are the parameters valid?"

### Explicit boundary? → Absent

No component, file, or module named as a boundary between deliberation and execution. Documentation does not mention boundary, checkpoint, or invariant concepts.

### Verification → Never

Neither compile-time nor runtime by default. Optional Pydantic schema validates form (types), never business rules.

### On violation → Silent

LLM hallucinates a tool name → `KeyError` exception. System crashes instead of refusing cleanly.

### L→S contamination open? → Yes

```
LLM (liquid) → tool_calls JSON → tool.invoke(args) → side effect (solid)
```

Direct, unguarded path.

## Positioning

LangChain doesn't solve this problem because it's not the problem it's trying to solve. LangChain optimizes for developer productivity — and does it well. The grid reveals what it leaves open, not what it does wrong.
