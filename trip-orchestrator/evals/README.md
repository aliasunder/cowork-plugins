# Evals

## Overview

Each eval tests whether the skill produces better trip planning outputs than a baseline agent without the skill. The same prompt and mock project files are given to two agents — one with SKILL.md loaded ("with_skill") and one without ("without_skill") — and both outputs are graded against the same assertions.

## Test scenarios

Defined in `evals.json`. There are 3 scenarios that cover distinct phases of a trip planning project:

### Eval 1: `new-trip-scaffolding`
**Phase tested:** Phase 1 (Define) — starting from nothing.
**Setup:** Empty workspace, no pre-existing files.
**Prompt:** User describes a 10-day Japan trip with partner Alex (bad knee, language anxiety, vegetarian + omnivore, $8K budget, 85K United miles).
**Tests:** Does the agent create the right file structure, put the right information in the right files, and avoid common pitfalls like dumping everything into CLAUDE.md?

### Eval 2: `mid-project-session-start-and-research`
**Phase tested:** Session continuity + Phase 6 (Restaurants).
**Setup:** Pre-seeded mock files simulating a mid-project state — CLAUDE.md (Phase 5/6), TASKS.md (active/done tasks), last session log. See the `files` array in evals.json for the exact mock content.
**Prompt:** User asks to check status and start Kyoto restaurant research.
**Tests:** Does the agent follow session-start protocol, create a research file (not a guide), present recommendations in day-by-day context, and avoid premature guide creation?

### Eval 3: `session-end-task-reconciliation`
**Phase tested:** Session close — the hardest skill behavior.
**Setup:** Pre-seeded mock files with unchecked restaurant subtasks in Active, a ryokan confirmation pending in Waiting On, and an open question about the ryokan in CLAUDE.md.
**Prompt:** User says they're done — finished Kyoto + Tokyo restaurants (approved), and the ryokan confirmed (ref KYO-8842).
**Tests:** Does the agent physically move tasks to Done (not just check them), close resolved waiting items, update open questions, and correctly leave the parent task open when one subtask (Osaka) is still pending?

## Assertions

Each eval has **positive** and **negative** assertions:

- **Positive assertions** check that something *should* happen (e.g., "session log written", "booking philosophy flagged"). Passes when the expected behavior is present.
- **Negative assertions** check that something should *not* happen (e.g., "no methodology in CLAUDE.md", "no premature guide creation"). Passes when the bad behavior is absent.

The full assertion list for each eval is in `evals.json` under `assertions.positive` and `assertions.negative`.

## Directory structure

```
evals/
  evals.json                        # Test definitions: prompts, mock files, assertions
  README.md                         # This file
  iteration-1/                      # First eval run
    benchmark.json                  # Aggregate scores
    new-trip-scaffolding/
      with_skill/
        grading.json                # Per-assertion pass/fail with evidence
        outputs/                    # Files the test agent created
          CLAUDE.md
          TASKS.md
          ...
      without_skill/
        grading.json
        outputs/
          ...
    mid-project-session-start-and-research/
      ...
    session-end-task-reconciliation/
      ...
  iteration-2/                      # After skill fixes (file boundaries, naming)
    ...
  iteration-2b/                     # Targeted re-test of eval 1 only
    ...
```

## How to read the results

### grading.json

Each run produces a `grading.json` with this structure:

```json
{
  "eval_id": 1,
  "eval_name": "new-trip-scaffolding",
  "configuration": "with_skill",
  "expectations": [
    {
      "text": "assertion-name",
      "passed": true,
      "evidence": "Specific quotes or line numbers from the output files"
    }
  ],
  "summary": {
    "passed": 10,
    "failed": 1,
    "total": 11,
    "pass_rate": 0.91
  }
}
```

The `evidence` field is the most useful part — it cites specific lines or quotes from the output files to justify the pass/fail decision.

### benchmark.json

Aggregates all runs in an iteration into a summary:

```json
{
  "run_summary": {
    "with_skill": { "pass_rate": { "mean": 0.94, "stddev": 0.09 } },
    "without_skill": { "pass_rate": { "mean": 0.76, "stddev": 0.17 } },
    "delta": { "pass_rate": "+0.18" }
  }
}
```

### outputs/

The actual files each test agent created. For eval 1, these are the full project scaffolding (CLAUDE.md, TASKS.md, traveler profiles, etc.). For evals 2-3, these are the updated files after the agent did its work.

Compare `with_skill/outputs/` vs `without_skill/outputs/` to see the qualitative difference the skill makes. The most revealing comparisons:

- **CLAUDE.md size and content** — with-skill should be lean (~80-200 lines, pure project state). Without-skill tends to dump methodology, session protocols, and full traveler details inline.
- **Task reconciliation** (eval 3) — with-skill physically moves completed tasks to Done. Without-skill often just checks them off in Active.
- **File naming** — with-skill uses `restaurant-kyoto.md`. Without-skill sometimes creates `research-restaurant-research-kyoto.md`.

## How grading works

Grading is done by a separate Claude agent that reads all output files and evaluates each assertion. The grading agent:

1. Reads every file in the `outputs/` directory
2. For each assertion, searches for evidence (specific lines, sections, file existence)
3. For positive assertions: passes if the expected behavior is found
4. For negative assertions: passes if the bad behavior is *absent*
5. Writes evidence quotes to justify each decision

The grading agent is strict — it cites line numbers and quotes. This makes it possible to audit the grades by reading the evidence and cross-referencing the output files.

## Iteration history

| Iteration | What changed | With-skill mean | Without-skill mean | Delta |
|-----------|-------------|----------------|-------------------|-------|
| 1 | Initial run, positive assertions only | 94% | 67% | +27% |
| 2 | Added negative assertions (11 total), file boundary rules, naming fix | 94% | 76% | +18% |
| 2b | Strengthened booking philosophy + people table guidance, re-tested eval 1 only | 91% (eval 1 only) | — | — |

The without-skill score improved in iteration 2 because the negative assertions catch different things — the without-skill agent happened to do well on research separation (eval 2) but still fails on task reconciliation (eval 3) and file structure (eval 1).
