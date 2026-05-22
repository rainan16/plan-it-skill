# Context Engineering for AI Agents — Reference

> From *"Context Engineering for AI Agents: Lessons from Building Manus"* by Yichao 'Peak' Ji (Manus, July 2025)

## 1. Design Around the KV-Cache

| Rule | Detail |
|------|--------|
| **Stable prompt prefix** | Even one token change invalidates the cache from that point onward. Avoid timestamps in system prompts. |
| **Append-only context** | Never modify previous actions or observations. Deterministic serialization. |
| **Cache breakpoints** | At minimum, place one at the end of the system prompt. |
| **Session routing** | Use session IDs to route requests consistently across distributed workers. |

## 2. Mask, Don't Remove

Dynamically adding/removing tools mid-iteration invalidates KV-cache and causes hallucinations. Use a state machine that masks token logits during decoding instead.

| Mode | Implementation |
|------|----------------|
| **Auto** | Prefill: `<|im_start|>assistant` |
| **Required** | Prefill: `<|im_start|>assistant<tool_call>` |
| **Specified** | Prefill: `<|im_start|>assistant<tool_call>{"name": "browser_` |

Design action names with consistent prefixes (e.g. `plan_*`, `shell_*`, `browser_*`).

## 3. Use the File System as Context

```
Context Window = RAM (volatile, limited)
Filesystem     = Disk (persistent, unlimited)
```

Compression strategies must be **restorable**: keep URLs even if page content is dropped, keep file paths when document contents are omitted.

## 4. Manipulate Attention Through Recitation

After ~50 tool calls, models forget original goals ("lost-in-the-middle"). Re-read `plan.md` before each decision to push goals into the recent attention window.

## 5. Keep the Wrong Stuff In

Failed actions + stack traces let the model implicitly update beliefs. Error recovery is a key indicator of true agentic behavior.

```
if action_failed: next_action != same_action
```

## 6. Don't Get Few-Shotted

Uniformity breeds fragility. Vary phrasing, formatting, and ordering to prevent pattern overfitting.

## 7. Stochastic Graduate Descent (SGD)

Expect to rebuild your framework multiple times. Manus rebuilt theirs 5 times.

## Key Quotes

> "Context window = RAM. Filesystem = Disk. Anything important gets written to disk."

> "KV-cache hit rate is the single most important metric for a production-stage AI agent."

> "Leave the wrong turns in the context."

> "Uniformity breeds fragility."
