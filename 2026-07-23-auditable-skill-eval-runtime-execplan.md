# Make the skill evaluation runtime auditable

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Make promotion-grade skill evaluations reproducible and auditable by requiring the executor model and reasoning effort at every promotion gate, separately recording the judge runtime, and passing every reported runtime choice to the actual Codex subprocess. A user can observe the result in the command line requirements, pure JSON report, and retained evidence for blocking runs.

## Scope

In scope are the `develop-skill-with-evals` runner, its deterministic tests, one focused self-evaluation case, the evaluation contract and schema, the skill instructions, root evaluation and CLI guides, command examples, the root `AGENTS.md`, and this living plan. Work begins in isolated baseline and candidate copies under `/tmp`; canonical skill files are promoted only after every required gate passes.

Out of scope are presets, a model matrix, automatic model escalation, changes to the user’s `config.toml`, commits, pushes, publication, changes to `.system`, and edits to the completed `2026-07-22-develop-skill-with-evals-execplan.md`.

Safety boundary: This task is limited to authorized, defensive maintenance of this repository. Do not provide offensive guidance or policy-bypassing instructions.

## Definitions

A promotion gate is `verify-change`, `stability`, or `run --all`; these commands decide whether candidate behavior is ready to copy into the canonical skill. An exploratory run is `run --case`, which may inherit the user’s Codex configuration. The executor is the Codex process that performs the case prompt. The judge is a separate Codex process that evaluates hidden semantic criteria. Runtime means the selected model and reasoning effort for either role. Mechanical checks are deterministic process and filesystem checks. Semantic judging is the model-based evaluation that runs only after all mechanical checks pass. `INCONCLUSIVE` is a blocking judge result requiring manual investigation or escalation.

## Existing Context

The canonical skill is `/home/renanfranca/.codex/skills/develop-skill-with-evals`. Its runner is `scripts/run_skill_evals.py`; deterministic tests are in `scripts/tests/test_run_skill_evals.py`; its contract and report schema are under `references/`; and its persisted self-evaluations are under `evals/`.

On 2026-07-23, the canonical baseline passed 15 unit tests, `python3 .system/skill-creator/scripts/quick_validate.py ./develop-skill-with-evals`, and `git diff --check`. Progress reporting already writes only to standard error and preserves JSON-only standard output. The report has a compatibility `model` field, but runtime values remain unresolved: `CODEX_MODEL` can change only the reported label, reasoning effort is absent, executor and judge runtimes are not separately reported, and semantic judging still runs after mechanical failure.

The immutable baseline and working candidate are `/tmp/auditable-skill-eval-runtime.NRwiun/baseline` and `/tmp/auditable-skill-eval-runtime.NRwiun/candidate`. The separate local context repository is `/home/renanfranca/.codex/skills/_temporary/codex-skills-ai-context`.

## Desired End State

The runner preserves `--model` and adds `--reasoning-effort`, `--judge-model`, and `--judge-reasoning-effort`. `verify-change`, `stability`, and `run --all` reject invocations without explicit executor model and reasoning effort. `run --case` can inherit configuration. Judge settings inherit executor settings unless explicitly overridden.

Executor and judge commands receive their actual runtime choices. Reasoning effort is passed as `-c model_reasoning_effort="<value>"`, with Codex responsible for validating model and effort names. `CODEX_MODEL` affects the exploratory executor subprocess when `--model` is omitted. Reports retain the top-level `model` field and add a root `runtime` object containing executor and judge model and reasoning effort. When mechanical checks fail, the judge object reports `executed: false` and `verdict: "SKIPPED"` and no semantic subprocess runs.

The focused `explicit-runtime-gates` case requires `promotion-commands.txt` and mechanically proves that `verify-change`, `stability`, and `run --all` include the agreed promotion runtime:

    --model gpt-5.6-sol
    --reasoning-effort medium
    --judge-model gpt-5.6-terra
    --judge-reasoning-effort medium

All repository guidance and examples explain the policy consistently. The root `AGENTS.md` remains approximately 200–400 words and identifies `_temporary/codex-skills-ai-context/` as the local repository exception for living ExecPlans.

## Milestones

### Milestone 1 - Establish the plan, isolation, and RED

#### Goal

Preserve the canonical source, document the baseline, add the focused case to the candidate before changing runtime behavior, and demonstrate that the baseline does not satisfy the new observable contract.

#### Changes

Create this ExecPlan in the nested context repository. Copy the canonical skill to the isolated baseline and candidate. Add `evals/cases/explicit-runtime-gates/{case.json,prompt.md}` to the candidate and register it in `evals/suite.json`.

#### Validation

Run:

    python3 /home/renanfranca/.codex/skills/develop-skill-with-evals/scripts/run_skill_evals.py run --skill /tmp/auditable-skill-eval-runtime.NRwiun/baseline --case explicit-runtime-gates --source working-tree --progress

If the baseline lacks the new case, run the candidate runner against the baseline-installed source through `verify-change`, because the case contract is always loaded from the candidate. The observable result must be baseline `FAIL`, not `PASS`, `ERROR`, or `INCONCLUSIVE`.

#### Acceptance Criteria

The case contains no hidden criteria in its prompt or fixture, requires `promotion-commands.txt`, and mechanically detects missing runtime flags. Baseline evidence demonstrates a real RED before runtime implementation.

### Milestone 2 - Implement and test auditable runtime behavior

#### Goal

Implement runtime resolution, gate validation, command propagation, reporting, and judge skipping in the isolated candidate.

#### Changes

Add focused deterministic tests to `scripts/tests/test_run_skill_evals.py`. Update `scripts/run_skill_evals.py`, `references/eval-result.schema.json`, `references/eval-contract.md`, `SKILL.md`, and `agents/openai.yaml` if its interface metadata becomes stale.

#### Validation

Run:

    RUN_SKILL_EVALS_SCRIPT=/tmp/auditable-skill-eval-runtime.NRwiun/candidate/scripts/run_skill_evals.py python3 -m unittest discover -s /tmp/auditable-skill-eval-runtime.NRwiun/candidate/scripts/tests -v
    python3 /home/renanfranca/.codex/skills/.system/skill-creator/scripts/quick_validate.py /tmp/auditable-skill-eval-runtime.NRwiun/candidate

#### Acceptance Criteria

Tests cover gate requirements, executor propagation, judge inheritance and overrides, `CODEX_MODEL`, root runtime reporting, skipped judges, JSON-only standard output, and progress behavior. Existing compatibility and progress tests remain green.

### Milestone 3 - Prove behavior and candidate stability

#### Goal

Show focused GREEN, valid baseline RED versus candidate GREEN, three stable repetitions, and a passing full candidate suite with explicit Sol and Terra runtimes.

#### Changes

Update the focused case or implementation only when evidence exposes a real defect. Record every blocking or surprising result in this plan before continuing.

#### Validation

Use all four runtime flags on:

    python3 /tmp/auditable-skill-eval-runtime.NRwiun/candidate/scripts/run_skill_evals.py run --skill /tmp/auditable-skill-eval-runtime.NRwiun/candidate --case explicit-runtime-gates --source working-tree --model gpt-5.6-sol --reasoning-effort medium --judge-model gpt-5.6-terra --judge-reasoning-effort medium --progress
    python3 /tmp/auditable-skill-eval-runtime.NRwiun/candidate/scripts/run_skill_evals.py verify-change --skill /tmp/auditable-skill-eval-runtime.NRwiun/candidate --case explicit-runtime-gates --baseline /tmp/auditable-skill-eval-runtime.NRwiun/baseline --model gpt-5.6-sol --reasoning-effort medium --judge-model gpt-5.6-terra --judge-reasoning-effort medium --progress
    python3 /tmp/auditable-skill-eval-runtime.NRwiun/candidate/scripts/run_skill_evals.py stability --skill /tmp/auditable-skill-eval-runtime.NRwiun/candidate --case explicit-runtime-gates --runs 3 --model gpt-5.6-sol --reasoning-effort medium --judge-model gpt-5.6-terra --judge-reasoning-effort medium --progress
    python3 /tmp/auditable-skill-eval-runtime.NRwiun/candidate/scripts/run_skill_evals.py run --skill /tmp/auditable-skill-eval-runtime.NRwiun/candidate --all --source working-tree --model gpt-5.6-sol --reasoning-effort medium --judge-model gpt-5.6-terra --judge-reasoning-effort medium --progress

#### Acceptance Criteria

Focused run, `verify-change`, stability, and full regression all report `PASS`. Any `INCONCLUSIVE` stops automatic work and is escalated manually. The report runtime matches the actual subprocess arguments.

### Milestone 4 - Update guides, promote, and verify

#### Goal

Align repository documentation, copy only the reviewed byte-equivalent candidate changes to canonical paths, and finish deterministic validation without rerunning model-consuming gates.

#### Changes

Update `AGENTS.md`, `README.md`, `CODEX_CLI.md`, and `EVALUATIONS.md`. Review the candidate-to-baseline patch, apply the same patch to canonical files, and verify canonical skill bytes match the validated candidate. Keep the previous ExecPlan unchanged.

#### Validation

Run:

    python3 -m unittest discover -s develop-skill-with-evals/scripts/tests -v
    python3 .system/skill-creator/scripts/quick_validate.py ./develop-skill-with-evals
    python3 -m json.tool develop-skill-with-evals/references/eval-result.schema.json
    python3 -m json.tool develop-skill-with-evals/evals/suite.json
    git diff --check
    wc -w AGENTS.md
    git status --short
    git -C _temporary/codex-skills-ai-context status --short

Also compare candidate and canonical skill trees while excluding generated `__pycache__` and `.pyc` files.

#### Acceptance Criteria

Canonical files match the validated candidate, all deterministic validations pass, `AGENTS.md` is approximately 200–400 words, the new plan is visible only to the nested repository, and the parent repository reports only intended guide and canonical implementation changes. No commit, push, publication, or user configuration mutation occurs.

## Progress

- [x] Required skill instructions and evaluation contract loaded.
- [x] Canonical baseline audited.
- [x] Fifteen baseline unit tests passed.
- [x] Baseline structural validation passed.
- [x] Isolated baseline and candidate created under `/tmp/auditable-skill-eval-runtime.NRwiun`.
- [x] Milestone 1 started.
- [ ] Focused case added before implementation.
- [ ] Baseline RED demonstrated.
- [ ] Milestone 1 completed.
- [ ] Milestone 2 started.
- [ ] Deterministic runtime tests added and shown RED.
- [ ] Candidate runtime behavior implemented.
- [ ] Focused deterministic tests GREEN.
- [ ] Milestone 2 completed.
- [ ] Milestone 3 started.
- [ ] Focused behavioral case GREEN.
- [ ] `verify-change` passed with explicit promotion runtime.
- [ ] Three stability repetitions passed.
- [ ] Complete candidate suite passed.
- [ ] Milestone 3 completed.
- [ ] Milestone 4 started.
- [ ] Documentation aligned.
- [ ] Reviewed candidate patch promoted byte-equivalently.
- [ ] Canonical deterministic and structural validation passed.
- [ ] Repository boundary checks passed.
- [ ] Milestone 4 completed.

## Decisions

- Decision: Use `gpt-5.6-sol` with `medium` reasoning for the executor and `gpt-5.6-terra` with `medium` reasoning for the judge at promotion gates.
  Rationale: Promotion evidence must identify and execute a deliberate, reproducible runtime for both roles.
  Date/Author: 2026-07-23 / User and Codex
- Decision: Require explicit executor model and reasoning effort for `verify-change`, `stability`, and `run --all`, but allow `run --case` to inherit configuration.
  Rationale: Promotion must be auditable while exploration should remain convenient.
  Date/Author: 2026-07-23 / User and Codex
- Decision: Let judge flags inherit executor settings unless the user supplies judge overrides.
  Rationale: This gives every judge a resolved runtime without forcing duplicate exploratory flags.
  Date/Author: 2026-07-23 / User and Codex
- Decision: Pass reasoning as `-c model_reasoning_effort="<value>"` and let Codex validate model and effort names.
  Rationale: The runner should forward runtime selection without duplicating Codex’s evolving validation rules.
  Date/Author: 2026-07-23 / User and Codex
- Decision: Preserve the top-level `model` report field and add a root `runtime` object for executor and judge.
  Rationale: Existing consumers retain compatibility while new consumers can audit both roles.
  Date/Author: 2026-07-23 / User and Codex
- Decision: Skip semantic judging after any mechanical failure and represent it as `executed: false`, `verdict: "SKIPPED"`.
  Rationale: Semantic model usage cannot repair a deterministic contract failure and would waste resources.
  Date/Author: 2026-07-23 / User and Codex
- Decision: Escalate `INCONCLUSIVE` manually.
  Rationale: Automatic escalation would hide a judgment failure and change runtime policy without review.
  Date/Author: 2026-07-23 / User and Codex
- Decision: Do not add presets, a model matrix, automatic escalation, or user `config.toml` changes.
  Rationale: These features exceed the requested auditable runtime boundary.
  Date/Author: 2026-07-23 / User and Codex

## Risks and Mitigations

- Risk: A reported runtime may differ from the subprocess runtime.
  Mitigation: Test exact fake Codex argv for executor, judge inheritance, judge overrides, and `CODEX_MODEL`, then report from the same resolved runtime objects used to build commands.
- Risk: Requiring flags on all runs would disrupt exploratory use.
  Mitigation: Enforce the requirement only on `verify-change`, `stability`, and `run --all`.
- Risk: A mechanical failure may still consume judge resources.
  Mitigation: Branch before judge invocation and test that only one fake Codex call occurs.
- Risk: Model-based gates can consume time or return `INCONCLUSIVE`.
  Mitigation: Run deterministic tests first, retain blocking artifacts, stop for manual review after `INCONCLUSIVE`, and never automatically switch models.
- Risk: Self-evolution may accidentally modify the canonical skill before validation.
  Mitigation: Edit only `/tmp/auditable-skill-eval-runtime.NRwiun/candidate` until every gate passes; promote by reviewed patch and compare bytes afterward.
- Risk: The parent repository may accidentally absorb local planning context.
  Mitigation: Keep plans in the nested context repository, preserve `_temporary/` as excluded parent scratch, and inspect both Git statuses before completion.
- Risk: Documentation examples may drift from enforced CLI behavior.
  Mitigation: Search every guide and skill instruction for promotion commands and model guidance, then run mechanical case assertions over the required examples.

## Validation Strategy

Validation proceeds from deterministic to model-consuming and back to final deterministic checks:

1. Confirm the 15-test baseline and structural validation.
2. Add the focused case and demonstrate baseline RED before implementation.
3. Add deterministic tests for CLI requirements, exact subprocess argv, inheritance, overrides, reporting, judge skipping, and progress.
4. Make candidate tests and structural validation GREEN.
5. Run focused GREEN, `verify-change`, three stability repetitions, and the full candidate suite with explicit Sol and Terra runtimes.
6. Validate JSON schemas and manifests, documentation consistency, `git diff --check`, guide word count, byte-equivalent promotion, and repository boundaries.

Standard output must remain parseable as a single JSON document in successful and runner-error paths. Progress and captured subprocess diagnostics remain on standard error or inside the JSON report according to the existing contract.

## Rollout and Recovery

Rollout is a reviewed patch from `/tmp/auditable-skill-eval-runtime.NRwiun/candidate` to the canonical repository after all candidate gates pass. Because promotion is byte-equivalent, model-consuming gates are not repeated against canonical files. Recovery before promotion is abandoning the candidate. Recovery after promotion is applying the inverse reviewed patch or restoring the affected canonical files from `/tmp/auditable-skill-eval-runtime.NRwiun/baseline`. No commit or push is part of rollout or recovery.

## Lessons Learned

- The original runner already separates standard output JSON from standard error progress and has deterministic fake Codex coverage, which provides a stable base for argv-level runtime assertions.
- The original `resolve_model` affects the report, but `codex_command` consults only `args.model`; therefore `CODEX_MODEL` is currently reported without changing the exploratory subprocess.
- The original evaluation flow invokes `run_judge` unconditionally after mechanical checks, so deterministic failures still consume semantic judge work.
- The root `AGENTS.md` is 351 words before tightening, already within the requested approximate range but lacking the nested context repository exception.
