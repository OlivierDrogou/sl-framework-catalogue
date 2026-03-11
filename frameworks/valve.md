# Valve

**Repo:** Under construction
**Language:** TypeScript strict
**License:** TBD
**Claim:** Generic agentic runtime with structural S/L separation

## Grid

| LLM→Tool direct? | Explicit boundary? | Boundary nature | Verification | On violation | L→S contamination open? |
|:-:|:-:|:-:|:-:|:-:|:-:|
| No | Yes (named) | Deterministic | Compile-time | Halt | No |

## Evidence

### LLM→Tool direct? → No

By construction in `types.ts`: a component `{ regime: 'liquid' }` cannot call a component `{ regime: 'solid' }`. The valve (`ValveContract`) is the only bridge. The TypeScript compiler rejects builds that bypass the valve.

### Explicit boundary? → Yes (named)

`valve.ts` is a dedicated component with a single responsibility: verify deterministic preconditions before authorizing the liquid→solid passage. Its interface is defined in `types.ts`:
```typescript
interface ValveContract {
    evaluate(recommendation: LiquidOutput): Promise<ValveDecision>;
}
```

### Boundary nature → Deterministic

The valve contains no probabilistic component. It verifies preconditions ordered by commitment level:
1. Structural validation (schema)
2. Authorization (permissions)
3. Business rules (domain constraints)
4. Transactional coherence (no double execution)

No LLM in the verification chain.

### Verification → Compile-time

TypeScript strict refuses to compile if the separation is violated. Contamination is detected before the software runs — not in production.

### On violation → Halt

```typescript
type ValveDecision =
    | { status: 'authorized'; toolName: string; validatedArgs: Record<string, unknown> }
    | { status: 'rejected'; reason: string; retryable: boolean }
    | { status: 'needs_confirmation'; toolName: string; reason: string; confirmationPrompt: string };
```

If a precondition fails: rejection, notification, escalation. No partial states.

Fail-closed by default: without a valve configured, tools with side effects are blocked.

### L→S contamination open? → No

By construction. The type system makes the direct path impossible. The difference between a "Do Not Enter" sign and a wall.

## Positioning

Valve is the only framework in this catalogue where the S/L separation is a compiler property, not a developer convention. Its current limitations are different from the others: it doesn't yet have a rule engine as mature as CORE's (92 rules, 7 engines). This is precisely the identified complementarity: CORE writes the protocols. Valve applies them at the boundary.

## Disclosure

Valve is the author's project. It is included in this catalogue for completeness and held to the same audit standard. The methodology is transparent — verify the claims against the source code.
