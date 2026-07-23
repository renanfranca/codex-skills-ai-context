# Build evaluation-driven skill development

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Create the personal `develop-skill-with-evals` skill so Codex can create or revise another skill through an observable RED, GREEN, stability, and regression workflow. The result includes a deterministic Python runner, versioned evaluation contracts and cases, and initial suites for both `refactor-design` and the new skill.

## Scope

In scope are the personal skill source, its runner and unit tests, evaluation cases for `develop-skill-with-evals`, and evaluation cases added to `refactor-design`. Out of scope are CI, plugin publication, commits, pushes, and changes to the system `skill-creator`.

## Definitions

A case is a directory containing `case.json`, a raw `prompt.md`, and optional fixture files. A baseline is an immutable earlier skill tree. A candidate is the tree under evaluation. `INVALID_RED` means the new behavioral case already passes on the baseline. `UNSTABLE` means repeated runs disagree. Mechanical checks are deterministic assertions over process results and filesystem effects; semantic checks are delegated to an independent evaluator through structured JSON.

## Existing Context

`/home/renanfranca/.codex/skills/develop-skill-with-evals` is the canonical personal skill and currently contains only the scaffold produced by `skill-creator`. `/home/renanfranca/.codex/skills/refactor-design` is an existing personal skill without persisted eval cases. Work on the new skill is isolated in `/tmp/develop-skill-with-evals.AZWMNM/candidate` until validation succeeds.

## Desired End State

The canonical new skill contains concise workflow instructions, a documented evaluation contract, a JSON result schema, a tested runner exposing `run`, `verify-change`, and `stability`, and six self-evaluation cases. `refactor-design` contains its five behavior cases plus trigger-classifier and implicit-smoke coverage. Both skills pass structural validation, runner unit tests pass, and the persisted suites can be invoked without exposing their oracles to executor sessions.

## Milestones

### Milestone 1 - Define contracts and RED tests

#### Goal

Specify the suite/case protocol and create failing unit tests for runner behavior before implementing it.

#### Changes

- [ ] Add `references/eval-contract.md` and `references/eval-result.schema.json`.
- [ ] Add `scripts/tests/test_run_skill_evals.py` covering parsing, isolation, Git baselines, mechanical checks, aggregation, and instability.
- [ ] Add self-evaluation cases before replacing the scaffold instructions.

#### Validation

- [ ] Command: `python3 -m unittest /tmp/develop-skill-with-evals.AZWMNM/candidate/scripts/tests`
- [ ] Expected result: tests initially fail because the runner does not exist.

#### Acceptance Criteria

- [ ] The failure is attributable to missing evaluation behavior rather than a broken test fixture.

### Milestone 2 - Implement runner and skill

#### Goal

Make focused tests green and encode the requested workflow in the candidate skill.

#### Changes

- [ ] Implement `scripts/run_skill_evals.py`.
- [ ] Replace scaffold `SKILL.md` and verify `agents/openai.yaml`.
- [ ] Add suite and cases under candidate `evals/`.

#### Validation

- [ ] Command: `python3 -m unittest /tmp/develop-skill-with-evals.AZWMNM/candidate/scripts/tests`
- [ ] Expected result: all unit tests pass.

#### Acceptance Criteria

- [ ] Every CLI operation returns schema-shaped JSON and blocking statuses have nonzero exits.

### Milestone 3 - Forward-test and promote

#### Goal

Use fresh agents to validate the isolated candidate, then promote it and add `refactor-design` cases.

#### Changes

- [ ] Forward-test candidate behavior with minimal, oracle-free prompts.
- [ ] Copy the validated candidate to the canonical skill source.
- [ ] Add `refactor-design/evals` without changing its normal skill context.

#### Validation

- [ ] Command: `quick_validate.py` for both skills.
- [ ] Command: runner unit tests and `git diff --check`.
- [ ] Command: real forward suite where available.

#### Acceptance Criteria

- [ ] Structural checks pass and any unavailable external execution is reported precisely.

## Progress

- [x] Official scaffold created and frozen as baseline.
- [x] Isolated candidate created.
- [x] Milestone 1 started.
- [x] Milestone 1 completed.
- [x] Milestone 2 started.
- [x] Milestone 2 completed.
- [x] Milestone 3 started.
- [x] Milestone 3 completed.

## Decisions

- Decision: Keep all self-modification work in a temporary candidate until independent validation.
  Rationale: The requested workflow explicitly protects the canonical source from unvalidated self-edits.
  Date/Author: 2026-07-22 / Codex
- Decision: Use JSON manifests plus raw prompt files and fixture directories.
  Rationale: This keeps executor input separate from expected contracts and supports deterministic mechanical checks.
  Date/Author: 2026-07-22 / Codex
- Decision: Install evaluated skills repo-scoped without their `evals/` directory.
  Rationale: Executors need the candidate instructions but must not discover manifests, judge criteria, or answer keys.
  Date/Author: 2026-07-22 / Codex
- Decision: Normalize only harness-owned `.eval-*` files and generated Python cache files out of stability path signatures.
  Rationale: Three semantically identical PASS runs differed only by whether Python emitted a test-module cache; production-path differences must still block promotion.
  Date/Author: 2026-07-22 / Codex

## Risks and Mitigations

- Risk: Real `codex exec` calls may be unavailable or costly in this environment.
  Mitigation: Test the runner with a fake executable and distinguish deterministic validation from optional real forward runs.
- Risk: Evaluation oracles may leak into executor context.
  Mitigation: Build executor prompts only from `prompt.md` and fixture content; reserve expected contracts for mechanical and judge phases.
- Risk: Editing personal skills is outside the current workspace.
  Mitigation: Work in `/tmp`, request explicit environment approval only for canonical promotion, and preserve the scaffold baseline.
- Risk: Interpreter cache files can make equivalent runs appear unstable.
  Mitigation: Exclude only `.eval-*`, `__pycache__`, and `.pyc` paths from verdict signatures; retain all production path differences.

## Validation Strategy

1. Run runner unit tests against a fake Codex executable.
2. Run structural validation for both skill directories.
3. Run focused persisted cases where the real Codex CLI supports the required structured execution.
4. Run `git diff --check` for repository-tracked additions.
5. Ask the user to run any broader external validation that cannot safely complete here.

## Rollout and Recovery

Promotion is a copy from the validated temporary candidate to the canonical personal skill. Recovery is replacing it with `/tmp/develop-skill-with-evals.AZWMNM/baseline` while that temporary directory exists. No Git commit or push is performed.

## Lessons Learned

- The canonical `refactor-design` skill currently has no `evals/` tree, so all persisted cases are additive and remain outside normal progressive-disclosure references.
- `codex exec` supports ephemeral sessions, workspace selection, output schemas, explicit model selection, and last-message files, so the runner can enforce the planned isolation without parsing conversational output.
- A fake Codex executable is sufficient to exercise runner orchestration deterministically; seven RED-first unit tests now cover the critical policy paths.
- The first real stability gate exposed a false divergence caused solely by `__pycache__`; a new RED test drove narrow normalization while retaining production-path comparison.
- Fresh forward tests demonstrated both branches: a new behavioral skill completed RED/GREEN/verification/stability/regression, while a metadata typo took the non-behavioral path without an invented RED.
- The final real `refactor-design` suite passed all six cases after the normalizer fix, and the focused case passed three stable repetitions.
- Canonical promotion was byte-compared to the validated candidates after removing generated Python caches; both skills passed `quick_validate.py` and all eight runner tests passed.
