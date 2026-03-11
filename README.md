# Where's the Valve?
## A Solid/Liquid Catalogue of AI Agent Frameworks

> *"A stochastic invariant is no longer an invariant."*
> — Drogou, 2026

An empirical catalogue classifying AI agent orchestration frameworks through the lens of **invariant integrity** — not productivity, not popularity, not feature count.

One question per framework: **does the architecture structurally prevent a probabilistic recommendation from becoming an irreversible action without deterministic verification?**

---

## The Grid

| Framework | LLM→Tool direct? | Explicit boundary? | Boundary nature | Verification | On violation | L→S contamination open? |
|-----------|:-:|:-:|:-:|:-:|:-:|:-:|
| **LangChain** (Python) | Yes | Absent | — | Never | Silent | Yes |
| **CrewAI** (Python) | Yes | Implicit | Absent | Never | Silent | Yes |
| **AutoGen / AG2** (Python) | Yes | Implicit | Absent | Never | Silent | Yes |
| **LlamaIndex** (Python/TS) | Yes | Absent | — | Never | Silent | Yes |
| **Semantic Kernel** (C#/Py/Java) | Config. | Implicit | Determ. if filter | Runtime | Config. | Partially |
| **CORE** (Python) | No | Yes (named) | Mixed (6+1) | Runtime | Halt | Partially |
| **Valve** (TypeScript) | No | Yes (named) | Deterministic | Compile-time | Halt | No |

Each cell is justified by traceable evidence in the corresponding framework card.

---

## Three Groups

**Group 1 — Open Path** (LangChain, CrewAI, AutoGen, LlamaIndex)
The four dominant frameworks are indistinguishable on the columns that matter for invariant integrity. The liquid→solid path is open by default.

**Group 2 — Bricks Available** (Semantic Kernel)
Provides an architectural mechanism (filter middleware) to build a valve. But filters are opt-in, not default.

**Group 3 — Structural Boundary** (CORE, Valve)
The only projects where the boundary is the primary objective. CORE has the most mature rule engine. Valve has compile-time enforcement.

---

## Framework Cards

- [LangChain / LangGraph](frameworks/langchain.md)
- [CrewAI](frameworks/crewai.md)
- [AutoGen / AG2](frameworks/autogen.md)
- [LlamaIndex](frameworks/llamaindex.md)
- [Semantic Kernel](frameworks/semantic-kernel.md)
- [CORE](frameworks/core.md)
- [Valve](frameworks/valve.md)

---

## Audit Protocol

Full protocol: [PROTOCOL.md](PROTOCOL.md)

1. Read the README — identify the architectural claim
2. Find the boundary — is there a component between the LLM and the tools?
3. Trace the liquid→solid path — follow the flow from LLM call to side effect
4. Identify error behavior — what happens on tool hallucination or invalid parameters?
5. Document acknowledged limits — does the framework recognize its own gaps?
6. Fill the card — column by column, with source code citations

Reproducible. Anyone can verify every cell independently.

---

## Contributing

Want to add a framework or challenge an assessment? See [CONTRIBUTING.md](CONTRIBUTING.md).

---

## Next Frameworks

- [ ] Agency Swarm
- [ ] Claude Flow
- [ ] SAFi
- [ ] ArbiterOS

---

## Theoretical Foundation

Drogou, O. (2026). *Séparation Solid/Liquid dans les Architectures d'Agents IA.*

- [Article series on Substack](https://mcflyresearch.substack.com)
- [Valve runtime](https://github.com/VALVE_REPO_LINK)
- [CORE — constitutional governance](https://github.com/DariuszNewecki/CORE)

## License

Content: CC BY 4.0
