# Make skill evaluations proportional to impact and cost

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` current as work advances.

## Purpose / Big Picture

Evolve `develop-skill-with-evals` so evaluation gates match the impact of a change, model session consumption is known before execution, and expensive evaluations stop until their estimated cost has explicit approval. Purely mechanical changes will use deterministic checks without model sessions. Behavioral changes retain RED, GREEN, and stability evidence for affected cases, with remaining suite regression only when the change is cross cutting.

## Scope

In scope are impact levels `static`, `deterministic`, `scoped`, and `cross-cutting`; side effect free `plan`; integrated `validate-change`; exact executor and judge session counts; a default approval limit of eight sessions; deterministic cases without an executor or judge; schemas, policy, metadata, root documentation, self evaluations, and full compatibility of `run`, `verify-change`, and `stability`.

Out of scope are token, duration, or financial estimates; automatic retries; commits, pushes, publishing, `.system/` edits; importing temporary candidate work from another plan; and changing progress behavior beyond converting its self evaluation to a deterministic case.

Safety boundary: This task is limited to authorized, defensive maintenance of this repository. Do not provide offensive guidance or policy bypassing instructions.

## Definitions

`static` means documentation, comments, formatting, or display text that cannot influence skill selection or behavior. `deterministic` means runner, schema, serialization, exit code, artifact, or other behavior completely observable by code. `scoped` means agent behavior whose affected cases can be explicitly enumerated. `cross-cutting` means selection, safety, central workflow, shared references, or any change whose reach cannot be enumerated confidently.

A model session is one executor or judge invocation. A case with both costs two sessions per execution. Cost approval is explicit authorization for the estimated session count; shell or sandbox approval is not cost approval. A stable signature is the runner's normalized combination of result, judge criteria, and mechanical failures.

## Existing Context

The canonical source is `/home/renanfranca/.codex/skills/develop-skill-with-evals`. Its runner is `scripts/run_skill_evals.py`, deterministic tests are in `scripts/tests/test_run_skill_evals.py`, contracts and schemas are under `references/`, and self evaluations are under `evals/`.

Before this change, the runner offered `run`, `verify-change`, and `stability`, reserved standard output for JSON, and used standard error for progress. The suite mixed semantic cases with `runner-progress-output`, which tested mechanical behavior through executor infrastructure. The baseline contained 15 deterministic unit tests.

The isolated root for this work is `/tmp/cost-aware-skill-evals.39XPLX`. `baseline-source` is the immutable canonical snapshot, `baseline-eval` is the baseline augmented only with new evaluation material needed to measure it, and `candidate` is the sole implementation workspace until promotion.

The separate ExecPlan `_temporary/codex-skills-ai-context/2026-07-23-auditable-skill-eval-runtime-execplan.md` remains incomplete and describes overlapping runtime changes. Canonical Git diff was empty when this work began, so none of that plan's isolated candidate changes are present or imported here.

## Desired End State

The runner adds:

    run_skill_evals.py plan --skill <candidate> --baseline <baseline> --impact <static|deterministic|scoped|cross-cutting> [--case <id>]...

`plan` creates no workspace, artifacts, or model sessions. Its JSON conforms to `references/eval-plan.schema.json` and reports impact, affected and regression cases, proposed steps and commands, baseline and candidate executions, executor and judge sessions, total sessions, approved limit, approval requirement, reasons, and warnings.

The runner also adds:

    run_skill_evals.py validate-change --skill <candidate> --baseline <baseline> --impact <deterministic|scoped|cross-cutting> [--case <id>]... [--approved-model-sessions <n>]

The default approved limit is eight. Estimates above the limit return code 2 with the plan on standard output before artifacts or model invocations. An explicit limit below the estimate behaves the same. `static` is planned but rejected for validation. `scoped` requires cases. `cross-cutting` requires directly affected cases and runs every remaining suite case once. Affected cases run baseline once and candidate three times. Baseline PASS becomes `INVALID_RED`; any candidate non PASS blocks; divergent candidate signatures become `UNSTABLE`; any remaining regression non PASS blocks; no failure is retried.

The case manifest accepts `kind: "deterministic"` while retaining current kinds. Deterministic cases require a mechanical observation, forbid enabled judges and executor configuration, do not require `prompt.md`, execute commands as direct argv with `SKILL_EVAL_SKILL_DIR` set to the absolute evaluated snapshot, record executor and judge as disabled, create no artificial response, and consume no model sessions. `runner-progress-output` uses this contract with an isolated checker.

Existing commands, schemas, exit behavior, artifacts, and progress remain compatible.

## Milestones

### Milestone 1: Preserve state and establish RED

Add `impact-gate-selection` to the isolated candidate and evaluable baseline, then add public CLI tests before implementation. Demonstrate that baseline does not recognize `plan`, follows the old semantic policy, and cannot run `runner-progress-output` as a deterministic case.

Validation:

    RUN_SKILL_EVALS_SCRIPT=/tmp/cost-aware-skill-evals.39XPLX/baseline-eval/scripts/run_skill_evals.py python3 -m unittest discover -s /tmp/cost-aware-skill-evals.39XPLX/candidate/scripts/tests -v

Acceptance requires reproducible failures caused only by missing behavior while canonical source remains unchanged.

### Milestone 2: Add planning and deterministic execution

Implement central impact validation, case selection, execution and session counts, plan construction, schema validation, and serialization. Add `eval-plan.schema.json`, extend result representation where needed, implement deterministic manifests, and convert `runner-progress-output`.

Validation uses the candidate unit suite, JSON schema parsing, and manual `plan` invocations. Acceptance requires no side effects from `plan`, exact counts at every impact, zero sessions for deterministic cases, early invalid manifest rejection, and parseable standard output.

### Milestone 3: Add integrated validation and budget enforcement

Implement `validate-change` by calling internal operations rather than recursively invoking the runner. Enforce the session limit before artifacts, run affected baseline once and candidate three times, compare stable signatures, and run nonduplicated remaining regression only for cross cutting changes.

Acceptance requires the default limit to allow eight and reject nine, explicit larger approval to allow execution, no duplicate affected regression, no retries, and compatibility of existing commands.

### Milestone 4: Align policy and documentation

Update `SKILL.md`, `references/eval-contract.md`, `agents/openai.yaml`, `EVALUATIONS.md`, and `CODEX_CLI.md`. The guidance classifies impact before gates, treats uncertainty as cross cutting, identifies underclassification as a workflow error, separates session approval from sandbox approval, recommends `plan`, documents the eight session default, forbids opportunistic reruns, and retains fresh agent validation before promotion.

Acceptance requires implementation, schemas, CLI messages, metadata, and documentation to express one consistent policy.

### Milestone 5: Bootstrap, review, promotion, and final verification

Run baseline once and candidate three times for `impact-gate-selection`, totaling eight nested model sessions, plus every deterministic check at zero sessions. Do not run the old complete semantic suite. Stop on FAIL, INCONCLUSIVE, or UNSTABLE rather than rerunning. Forward test once with a fresh agent using only the isolated candidate and a realistic mechanical runner task.

After GREEN, run the post implementation design review, inspect the candidate patch, verify canonical source is unchanged, apply only reviewed candidate changes to canonical paths, compare bytes excluding caches, and repeat local deterministic gates.

## Progress

- [x] Required skill instructions and evaluation contract loaded.
- [x] Existing runtime ExecPlan inspected for overlap.
- [x] Canonical source confirmed free of tracked overlapping edits.
- [x] Isolated root, immutable baseline, evaluable baseline, and candidate created.
- [x] This ExecPlan saved with the real isolated root.
- [x] Focused self evaluation and public CLI tests added.
- [x] Baseline CLI RED demonstrated: the frozen runner rejected `plan` as an unknown operation while its other 15 tests remained green.
- [x] `plan`, schema, and deterministic cases implemented.
- [x] `validate-change`, budget, and aggregate result implemented.
- [x] Focused and full deterministic tests GREEN: 29 tests pass in the isolated candidate.
- [x] Policy, contract, metadata, and candidate copies of root documentation aligned.
- [x] Limited semantic bootstrap completed. After two diagnosed baseline attempts consumed four sessions, the replacement public-path case produced baseline `FAIL` and three stable candidate `PASS` results in eight sessions under the user's revised cumulative approval.
- [x] Fresh agent validation completed: it independently selected deterministic impact, `status-stream`, zero sessions, no regression, and no approval requirement.
- [x] Post GREEN design review completed; snapshot consistency and static planning without a suite were corrected and revalidated.
- [x] Reviewed candidate promoted without unrelated changes; canonical skill bytes match the isolated candidate except ignored caches.
- [x] Canonical deterministic gates completed: 29 unit tests, structural validation, both schemas, public plan schema validation, deterministic progress case, and diff checks passed.
- [x] Final results, residual risks, and lessons recorded.

## Decisions

- 2026-07-23, User: Use impact levels instead of fixed coverage groups.
- 2026-07-23, User: Implement policy, estimator, and integrated validation.
- 2026-07-23, User: Permit deterministic cases with no model executor.
- 2026-07-23, User: Require explicit approval only above eight estimated sessions.
- 2026-07-23, User: Limit bootstrap to one baseline and three candidate runs for one semantic case, without the old complete semantic regression.
- 2026-07-23, Codex: Count sessions rather than claiming unreliable token, duration, or price estimates.
- 2026-07-23, Codex: Preserve existing commands and introduce separate operations.
- 2026-07-23, Codex: Classify uncertain or unbounded reach as cross cutting.
- 2026-07-23, Codex: Do not import unpromoted runtime work from the overlapping ExecPlan because canonical source contains none of it.
- 2026-07-23, Codex: For `deterministic` impact with no explicit cases, select every deterministic suite case; explicit selections must all be deterministic.
- 2026-07-23, Codex: Materialize baseline and candidate snapshots before integrated validation so `SKILL_EVAL_SKILL_DIR` never points at mutable canonical source.
- 2026-07-23, Codex: Stop integrated validation on the first non PASS candidate result; the three planned runs are stability evidence, not retries after failure.
- 2026-07-23, Codex: Fingerprint normalized suite manifests and compare the approved plan with the materialized snapshot before invoking a model.
- 2026-07-23, Codex: Permit `static` planning without `evals/suite.json`, since its only proposed gates are structural.
- 2026-07-23, Codex: Give proven mechanical failure precedence over an inconclusive judge verdict; observable contract failure is `FAIL`, not missing evidence.
- 2026-07-23, Codex: Replace a terminology-sensitive self evaluation with a public-path case that requires the new `plan` operation to execute successfully against an isolated target suite.
- 2026-07-23, Codex: Proposed validation commands use the currently executing runner, not a nonexistent runner under the target skill.
- 2026-07-23, User: Approve the cumulative additional sessions needed after the diagnosed `INCONCLUSIVE` and `INVALID_RED` bootstrap attempts, plus one fresh-agent session.
- 2026-07-23, Codex: Promote only the byte-equivalent reviewed candidate files and candidate root documentation; exclude caches, retained blocking artifacts, and temporary fresh-agent fixtures.

## Risks and Mitigations

- Risk: The bootstrap exception conflicts with the repository's previous full regression rule.
  Mitigation: Treat the user's explicit instruction as a one time migration exception, record it here, and retain focused RED, GREEN, stability, deterministic regression, and fresh agent evidence.
- Risk: The incomplete runtime plan touches the same runner.
  Mitigation: Work from current canonical source in a new root and do not copy its candidate.
- Risk: A user can underclassify impact to reduce cost.
  Mitigation: Make reasons and warnings visible, require direct cases for scoped and cross cutting work, and document uncertainty as cross cutting.
- Risk: Session count does not predict tokens, time, or money.
  Mitigation: State that limitation and avoid false financial precision.
- Risk: A deterministic label can hide semantic behavior.
  Mitigation: Require entirely code observable checks and reject executor or enabled judge configuration.
- Risk: Model results can vary.
  Mitigation: Compare three candidate signatures and stop without opportunistic retry.
- Risk: The semantic judge may lack file evidence needed to distinguish RED from missing evidence.
  Mitigation: The first real baseline run exposed this risk. The corrected hidden mechanical command serializes `evaluation-plan.json` into judge evidence. Do not rerun without explicit approval for the revised cumulative session count.
- Risk: User changes could be overwritten during promotion.
  Mitigation: compare canonical, baseline source, and candidate immediately before a file scoped patch.

## Validation Strategy

Unit tests cover parser behavior, plan schema and contents, all four impacts, absence of planning side effects, deterministic manifests, disabled executor and judge, zero session costs, affected baseline and candidate repetitions, exact eight session counts, instability, cross cutting regression without duplicates, early budget refusal, larger explicit approval, no retries, JSON and progress compatibility, and all existing operations.

Run against candidate and then canonical:

    python3 -m unittest discover -s develop-skill-with-evals/scripts/tests -v
    python3 .system/skill-creator/scripts/quick_validate.py ./develop-skill-with-evals
    python3 -m json.tool develop-skill-with-evals/references/eval-plan.schema.json
    python3 -m json.tool develop-skill-with-evals/references/eval-result.schema.json
    git diff --check
    git status --short

Exercise candidate planning and validation:

    python3 /tmp/cost-aware-skill-evals.39XPLX/candidate/scripts/run_skill_evals.py plan --skill /tmp/cost-aware-skill-evals.39XPLX/candidate --baseline /tmp/cost-aware-skill-evals.39XPLX/baseline-eval --impact scoped --case impact-gate-selection

    python3 /tmp/cost-aware-skill-evals.39XPLX/candidate/scripts/run_skill_evals.py validate-change --skill /tmp/cost-aware-skill-evals.39XPLX/candidate --baseline /tmp/cost-aware-skill-evals.39XPLX/baseline-eval --impact scoped --case impact-gate-selection --progress

Expected semantic estimate is eight sessions with `approval_required: false`, baseline valid RED, and three stable candidate PASS results.

## Rollout and Recovery

Promote with a reviewed file scoped patch from candidate, never by copying the temporary root wholesale. Exclude caches, generated artifacts, and this planning repository from the normal contribution. Before promotion compare candidate to baseline, remove unrelated changes, and stop if canonical differs from `baseline-source`.

Before promotion, recovery is abandoning the candidate. After promotion, recovery is applying the inverse reviewed patch or restoring only affected files from `baseline-source`. Never use `git reset --hard`.

## Lessons Learned

- The suite currently combines expensive semantic regressions with cheap mechanical guarantees.
- RED, GREEN, and stability remain valuable but do not justify cases incapable of observing a change.
- Model sessions can be counted beforehand; tokens, price, and duration cannot be predicted with equal confidence.
- Sandbox approval does not prove acceptance of evaluation cost.
- Repeating variable results until PASS weakens evidence; instability must be explicit.
- The incomplete runtime plan has no tracked canonical implementation to preserve, so overlap is documentary and future facing rather than a current working tree merge.
- A deterministic case can still exercise nested runner behavior with a fake executor; what makes it deterministic is that the outer gate is fully code observed and invokes no model.
- Budget refusal must happen before even the operation root is created, so planning and budget calculation cannot reuse helpers that eagerly allocate artifact directories.
- The first sandboxed bootstrap attempt established no model session because Codex initialization failed. The authorized unsandboxed attempt established one executor and one judge session, then returned `INCONCLUSIVE` because the judge payload omitted the generated plan contents.
- A required output path is insufficient semantic evidence by itself. When a judge must inspect generated content, a hidden mechanical command can serialize only the relevant artifact into the existing mechanical evidence without leaking criteria to the executor.
- Post GREEN design review found a time of check versus time of use risk between planning and evaluation. Rebuilding the plan from the materialized snapshot and comparing its fingerprint prevents execution against different case contracts.
- The first corrected case still produced `INVALID_RED`: the baseline already chose deterministic commands and zero sessions despite using the old `behavioral` label. A useful RED must distinguish observable capability, not vocabulary.
- Planning an arbitrary target skill exposed that proposed commands cannot assume the target contains `scripts/run_skill_evals.py`; they must reference the runner that built the plan.
- The final self evaluation observed the new public path directly: the baseline runner rejected `plan` with exit code 2, while all three candidate runs emitted schema-valid deterministic plans with zero model sessions and no regression cases.
- The fresh agent independently used the candidate planning workflow, selected only `status-stream`, reported zero executor and judge sessions, and required no approval. It also correctly warned that the placeholder case must assert stdout, stderr, and exit code before real validation.
- The completed bootstrap used 12 established nested model sessions across three diagnosed attempts, within the user's revised cumulative approvals, plus one fresh-agent session. The earlier sandbox attempt failed before a model session initialized.
- Canonical validation confirmed no generated responses, retained artifacts, or temporary absolute paths leaked into versioned skill or guide files.
