# Audit Protocol

## How to analyze a framework

For each repository, follow this exact procedure:

### Step 1 — Read the README

Identify the architectural claim. What does the project promise?
Note: productivity-focused claims are legitimate. The audit doesn't judge the objective — it reveals what the objective leaves open.

### Step 2 — Find the boundary

Is there a component, file, or module whose explicit role is to stand between the LLM's recommendation and the tool's execution?

Look for: middleware, filters, gates, validators, checkpoints, approval mechanisms.
Not sufficient: Pydantic schemas (validate form, not business rules), Docker containers (isolate environment, not verify content), human-in-the-loop options (probabilistic, not deterministic).

### Step 3 — Trace the liquid→solid path

Follow the execution flow from the moment the LLM produces a tool call to the moment the tool executes with side effects. Document every checkpoint (or absence of checkpoint) along the way.

Produce a flow diagram:
```
LLM → [what happens here?] → tool.execute(args) → side effect
```

### Step 4 — Identify error behavior

What happens when:
- The LLM hallucinates a tool name that doesn't exist?
- The LLM passes invalid parameters?
- The LLM proposes a dangerous action?

Possible answers: structured rejection, exception, silent execution, crash.

### Step 5 — Document acknowledged limits

Does the framework recognize its own gaps? Look for:
- Security documentation
- Known limitations sections
- Bug reports related to tool execution safety
- Warnings in the documentation

### Step 6 — Fill the card

One markdown file per framework in `frameworks/`. Fill every column with:
- The answer
- The evidence (code citation, documentation quote, bug report link)
- A positioning note (neutral — what the framework solves, what it leaves open)

## Column definitions

| Column | Question | Possible answers |
|--------|----------|-----------------|
| LLM→Tool direct? | Can the LLM trigger tool execution without a deterministic checkpoint? | Yes / No / Configurable |
| Explicit boundary? | Is the deliberation/execution separation named in the architecture? | Yes (named) / Implicit / Absent |
| Boundary nature | Is the checkpoint deterministic or probabilistic? | Deterministic / Probabilistic / Mixed / Absent |
| Verification | When is contamination detected? | Compile-time / Runtime / Never |
| On violation | What happens when a precondition fails? | Halt / Log + continue / Silent |
| L→S contamination open? | Is the liquid→solid path unguarded? | Yes / No / Partially |

## Tone

Neutral. Every framework optimizes for something. The grid reveals what they leave open, not what they do wrong. Positioning notes must acknowledge the framework's strengths before noting the gaps.
