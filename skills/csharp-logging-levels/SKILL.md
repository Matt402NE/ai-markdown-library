---
name: csharp-logging-levels
description: Apply standardized logging level guidance when writing or reviewing C# code that uses Microsoft.Extensions.Logging. Use when generating ILogger calls, choosing between LogTrace/LogDebug/LogInformation/LogWarning/LogError/LogCritical, writing exception handling with logging, creating Repository/Service/Orchestrator classes, or reviewing log levels in existing code. Ensures consistent log level selection across layers.
---
---


# ILogger Logging Level Guidance

Apply these rules when writing `ILogger` calls in C# code using `Microsoft.Extensions.Logging`. For detailed rationale, examples, and FAQ, consult `references/guidance.md`.

## Level Selection by Layer

| Layer        | Primary Levels                          | Notes                                                                                                                                   |
| ------------ | --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Repository   | Trace, Debug                            | Never log Information+. Let unrecoverable exceptions propagate.                                                                         |
| Service      | Debug                                   | No Information+ when called by an Orchestrator. If topmost layer, act as Orchestrator.                                                  |
| Orchestrator | Information, Warning, Error, Critical   | Every Information entry must be a milestone. Log workflow bookends at Information.                                                      |
| Loops        | Trace (per-item), Debug (batch summary) | Progress checkpoints at Information for long-running batches (e.g., every 10%). Error only for irrecoverable independent item failures. |
|              |                                         |                                                                                                                                         |

## Decision Flow

When writing a log statement, apply the first matching rule:

1. Application/system in jeopardy → **Critical**
2. Current operation failed, cannot recover → **Error**
3. Unexpected condition, system continued → **Warning**
4. Orchestrator summarizing a milestone (workflow bookend, state transition, integration completion, progress checkpoint, aggregate result, threshold crossing) → **Information**
5. Service-level decision, outcome, or diagnostic → **Debug**
6. Repository or loop per-item detail → **Trace**

## Exception Rules

- **Unhandled:** Error (single operation failed) or Critical (system-wide / data integrity).
- **Handled with recovery:** Debug or Trace. Never Warning or Error.
- **Exhausted retries with fallback:** Warning.
- **Business rule violations:** Debug.
- **Single Responsibility:** Only the workflow boundary layer logs Error. Lower layers log Debug context, then propagate.

## Message Templates

- Use structured templates: `LogInformation("Processing order {OrderId}", orderId)` — never string interpolation.
- Present tense for in-progress: `"Processing order {OrderId}"`
- Past tense for completions: `"Completed order {OrderId}"`
- Always include at least one contextual identifier (`OrderId`, `CorrelationId`, etc.).
- Pass the exception object as the first parameter to preserve stack traces.

## Anti-Patterns

- Sensitive data (passwords, tokens, PII) in log entries
- Information+ inside loops for routine per-item outcomes
- Validation failures or handled exceptions at Warning or Error
- Duplicate Error entries across layers for the same failure
- High-cardinality structured properties (raw payloads, user agents, serialized objects)