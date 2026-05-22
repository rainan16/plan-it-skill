# plan-it

An OpenCode skill that turns [question-me](https://github.com/rainan16/question-me-skill) session output into a concrete, phased execution plan.

After you've grilled your idea with `question-me`, say **"plan it"** and plan-it writes `task_plan.md` with phases, decisions, open questions, and error tracking — ready for execution.

Built on context engineering principles from Manus (file-as-memory, attention recitation, error retention).

```
Question-me grills your idea → Refined intent + key decisions → Plan-it → task_plan.md
```

## Install

```bash
# project-level
cp -r .agents/skills/plan-it /path/to/your/project/.opencode/skills/plan-it

# or global
cp -r .agents/skills/plan-it ~/.config/opencode/skills/plan-it
```

## Quick start

1. Say `question me` to clarify your idea
2. When it's done, say `plan it` — and optionally paste the output
3. Plan-it creates a phased `task_plan.md` with decisions table and error log

## Benchmark (iteration 1)

| Variant | Pass rate |
|---------|:---------:|
| With skill | 24/24 (100%) |
| Baseline (no skill) | 4/24 (16.7%) |

3 test cases across code, infra, and DevOps domains. See `evals/baseline-results.json`.
