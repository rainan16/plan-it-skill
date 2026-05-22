# Rerunning evals

Run the 3 test prompts with and without the skill, then compare against the baseline.

## Setup

```bash
mkdir -p evals/results/iteration-N
```

## Run each prompt

For each eval in `evals/evals.json`:

1. **With skill**: point the agent at `.agents/skills/plan-it/SKILL.md`, give the prompt. Save outputs to `evals/results/iteration-N/{eval-name}/with_skill/outputs/`
2. **Without skill** (baseline): same prompt, no skill loaded. Save to `evals/results/iteration-N/{eval-name}/without_skill/outputs/`
3. Save `timing.json` in each run directory (`total_tokens`, `duration_ms`)

## Grade

Grade each run against the assertion list in `evals/evals.json`. Write results to `grading.json`:

```json
{
  "eval_id": 1,
  "eval_name": "direct-paste-expense-tracker",
  "variant": "with_skill",
  "assertions": [
    {"text": "Output file is named task_plan.md", "passed": true, "evidence": "..."}
  ]
}
```

### Best discriminating assertions

These are the ones baselines consistently fail (highest signal):

| Assertion | Baseline pass rate |
|-----------|:------------------:|
| Decisions Made table populated | 0/3 |
| Phase 1 = in_progress, rest = pending | 0/3 |
| Errors Encountered table present | 0/3 |
| Current Phase tracking present | 0/3 |

Stable passers (both conditions pass): "Key Questions carried over".

## Compare

Compare pass rates against `evals/baseline-results.json`:

- **With skill**: 24/24 (100%)
- **Without skill**: 4/24 (16.7%)
- **Delta**: +83.3 percentage points

Use `skill_aggregate_benchmark` to generate `benchmark.json`, then `skill_serve_review` to launch the comparison viewer.
