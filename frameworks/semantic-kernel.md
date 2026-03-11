# Semantic Kernel

**Repo:** [github.com/microsoft/semantic-kernel](https://github.com/microsoft/semantic-kernel)
**Language:** C#, Python, Java
**License:** MIT
**Claim:** Enterprise-grade middleware — rapid LLM integration into applications

## Grid

| LLM→Tool direct? | Explicit boundary? | Boundary nature | Verification | On violation | L→S contamination open? |
|:-:|:-:|:-:|:-:|:-:|:-:|
| Configurable | Implicit | Determ. if filter | Runtime | Configurable | Partially |

## Evidence

### LLM→Tool direct? → Configurable

By default with `FunctionChoiceBehavior.Auto()`, the LLM chooses when and which tools to call, and SK executes them automatically. The path is direct.

But SK provides a **Filter** system (middleware) at three levels:
- `IPromptRenderFilter` — intercepts prompt before sending to LLM
- `IFunctionInvocationFilter` — intercepts every function call
- `IAutoFunctionInvocationFilter` — intercepts calls triggered by the LLM

With a filter, the developer can inspect the function name, arguments, and **block execution** by not calling `next(context)`:

```csharp
public sealed class FunctionInvocationFilter : IFunctionInvocationFilter
{
    public async Task OnFunctionInvocationAsync(
        FunctionInvocationContext context,
        Func<FunctionInvocationContext, Task> next)
    {
        // Inspect context.Function.Name and context.Arguments
        // NOT calling next() = block execution
        await next(context);
    }
}
```

`IAutoFunctionInvocationFilter` has access to chat history, request count, and function count — enough to implement a valve.

### Explicit boundary? → Implicit

Filters are a mechanism, not a named boundary. They're presented as enterprise middleware (logging, telemetry, caching). Their use as a valve is possible but not prescribed.

### Boundary nature → Deterministic (if filter implemented) / Absent (by default)

When a filter is implemented, verification is deterministic — it's C#/Python code, not an LLM. But by default, without a filter, the path is completely open.

### Verification → Runtime

Filters execute at runtime. The C# or Python type system doesn't refuse to compile an agent without a filter.

### On violation → Configurable

With a filter: halt possible (don't call `next()`), result override, retry. Without a filter: silent, like the other frameworks.

**Bug #12998** ([github.com/microsoft/semantic-kernel/issues/12998](https://github.com/microsoft/semantic-kernel/issues/12998)): `IAutoFunctionInvocationFilter` doesn't fire on all connectors (Gemini doesn't trigger it). The boundary, even when implemented, is fragile and provider-dependent.

### L→S contamination open? → Partially

Open by default. Closable by the developer via filters. But filters are opt-in (not opt-out), and bug #12998 shows they're not reliable across all connectors.

## Positioning

Semantic Kernel is the only mainstream framework (outside CORE and Valve) that provides an **architectural mechanism** for building a valve. The filter middleware pattern is correct — intercept the function call before execution. But the framework provides the bricks, not the wall.

The difference:
- Providing bricks (Semantic Kernel)
- Providing the wall (CORE)
- Providing a wall the compiler refuses to bypass (Valve)

**Note:** SK and AutoGen are merging into the "Microsoft Agent Framework." If SK's filters survive the merger, the new framework will be better positioned than AutoGen alone. If the filters are sacrificed to simplicity — as AutoGen's caller/executor split was in v0.4 — Microsoft will have repeated the same mistake twice.
