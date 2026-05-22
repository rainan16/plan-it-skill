---
name: plan-it
description: Turn a refined idea or question-me output into a concrete, phase-based execution plan. Use this skill whenever the user says "plan it", "make a plan", "create a plan", "turn this into a plan", or hands you structured output from a question-me/grill-me/intent-refinement session and asks you to plan the work. 
license: Apache-2.0
metadata:
  author: rainan16
  version: "1.0.0"
---

You are producing an execution plan from a refined idea or question-me session output. Your job is to translate the clarified intent into a structured, phase-based `task_plan.md` and a brief summary for the user.

## When to use this skill

This skill activates when the user has already clarified their intent (typically through a question-me/grill-me session) and now wants an actionable task_plan.md with numbered phases, decisions, and error tracking. You should trigger when the user explicitly asks you to make a plan, and you can see refined intent, key decisions, or next steps in the conversation.

The user may hand you the intent in one of two ways:
- **Pasted directly into the conversation** — the refined intent, key decisions, open questions, and suggested next steps are in the chat
- **As a file reference** — they say "I ran question-me/grill-me on my idea, [describe briefly], the output is in <path>"

In either case, read the output carefully before planning.

Plan-it should NOT trigger for simple to-do lists or quick one-step tasks that don't need phased execution.

## Core principles (read the reference)

Read `references/CONTEXT-ENGINEERING-REFERENCE.md` for the full context engineering playbook. The key ideas that matter for planning are:

- **File system as context**: The `task_plan.md` is your "working memory on disk". Keep it updated as the plan progresses. The user will execute the plan later, possibly over many LLM sessions, so the file must be self-contained.
- **Recitation**: Each phase should be written so the agent executing it can re-read the plan and immediately know: goal, current phase, remaining phases, key decisions made.
- **Keep the wrong stuff in**: Track errors and failed approaches in the Errors Encountered table so they aren't repeated.
- **Avoid few-shot ruts**: Vary phrasing and formatting between phases to prevent pattern overfitting.

## Output format

You produce **two things**:

### 1. A `task_plan.md` file

Write this to the user's working directory. Use the template at `references/task_plan.md` as your starting point. Adapt the phases to match the specific task — the template's 5 phases (Requirements, Planning, Implementation, Testing, Delivery) are a sensible default for software projects, but you should rename, split, or merge them as needed.

Every `task_plan.md` MUST include these sections:
- **Goal** — one clear sentence describing the end state (directly from the question-me refined intent)
- **Current Phase** — always start at Phase 1
- **Phases** — 3–7 phases, each with:
  - A descriptive title
  - Concrete checklist items
  - A status: `pending`, `in_progress`, or `complete`
  - Status for Phase 1 starts at `in_progress`, rest at `pending`
- **Key Questions** — carry over from question-me's open questions, plus any new ones
- **Decisions Made** — populate from question-me's key decisions table
- **Errors Encountered** — start empty, the template table is ready for the executor to fill

### 2. A brief execution summary (printed to the conversation, not a file)

A short message to the user covering:
- What the task is (one-liner)
- How many phases the plan has and what they are
- Where the `task_plan.md` was written
- Any open questions that still need answering before execution starts
- Any risks or ambiguities you noticed

## Deriving phases from question-me output

The question-me output gives you:
- **Refined intent** → becomes the Goal
- **Key decisions** → populate the Decisions Made table
- **Open questions** → populate the Key Questions section (they need answers before or during execution)
- **Suggested next steps** → these are the seed for your phases. Expand them into full phases with detailed checklist items. A single "Implement the X" step might become a full phase with 3-5 sub-tasks.

### Phase design guidelines

- Phase 1 should ALWAYS be some form of setup/understanding (even if the idea is already clarified, the executor needs to orient themselves)
- Each phase should be completable in a single LLM session if possible
- Split long phases; merge trivial ones
- Name phases with consistent prefixes where it makes sense (e.g., `Phase 1: Setup`, `Phase 2: Core Implementation`, `Phase 3: Testing`)
- For code projects, use the familiar pattern: Setup → Core Feature X → Feature Y → Testing → Polish/Delivery
- For non-code tasks (research, writing, event planning), adapt the phases to the domain

## After writing the plan

Tell the user you've created the plan. Offer to:
1. Review and adjust any phases
2. Start executing the first phase
3. Save additional reference files (`findings.md` for discoveries, `progress.md` for the session log) if the task warrants a full template set

For complex tasks, suggest also creating `findings.md` and `progress.md` from the corresponding templates in `references/`. For simpler tasks, `task_plan.md` alone is sufficient.
