# File reference

```
plan-it-skill/
├── AGENTS.md                        # This file — agent entry point
├── README.md                        # Human-facing overview
├── LICENSE                          # Apache 2.0
├── NOTICE                           # Notices
├── docs/
│   ├── architecture.md              # This file
│   └── evals.md                     # How to rerun evals
├── evals/
│   ├── evals.json                   # 3 test prompts + 8 assertions each
│   └── baseline-results.json        # Iteration 1 benchmark (merged single file)
└── .agents/skills/plan-it/
    ├── SKILL.md                     # Core instructions (read this first)
    └── references/
        ├── task_plan.md             # Output template (Goal → Phases → Decisions → Errors)
        ├── findings.md              # Knowledge base template (2-action rule)
        ├── progress.md              # Session log template (5-question reboot)
        └── CONTEXT-ENGINEERING-REFERENCE.md  # Manus principles
```

## Component relationships

```
question-me (separate skill)
       │
       │  Refined intent + key decisions + open questions
       ▼
plan-it (this skill)
       │
       │  Reads SKILL.md + CONTEXT-ENGINEERING-REFERENCE.md
       │  References task_plan.md template
       ▼
task_plan.md  ←  written to user's working directory
```

## Key patterns

- **KV-cache**: SKILL.md prompt prefix must stay stable. No timestamps in system prompt.
- **Append-only context**: Never modify previous actions/observations.
- **File system as context**: task_plan.md is working memory on disk. Context = volatile RAM.
- **Recitation**: Rewrite task_plan.md after each phase to keep goal in recent attention window.
- **Error retention**: Leave failures in context — each mistake teaches the model.
