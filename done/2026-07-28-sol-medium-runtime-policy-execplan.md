# Remover economic_runtime e recomendar Sol medium

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Remove the advisory `economic_runtime` object from skill evaluation plans because it duplicates repository guidance without controlling the runtime that actually executes. Keep runtime selection explicit and document `gpt-5.6-sol` with medium reasoning effort as this repository's recommendation for both executor and judge.

The observable result is that `plan` JSON contains `runtime` and its audit fields but no `economic_runtime`. Repository instructions recommend Sol medium while preserving zero model sessions for static and deterministic work and preserving every explicit user choice.

## Scope

In scope are `develop-skill-with-evals`, its public plan schema and contract, its deterministic tests and cases, `AGENTS.md`, `EVALUATIONS.md`, and `CODEX_CLI.md`.

Historical evaluation reports, pricing facts, model comparison records, propagation tests that deliberately use different model identifiers, `.system`, other skills, and unrelated untracked files are out of scope. Do not stage, commit, push, publish, or run a model session.

## Definitions

`economic_runtime` is the current advisory plan object that recommends models but never fills runtime arguments. `runtime` is the auditable plan object that records resolved executor and judge values and their sources. Sol medium means model `gpt-5.6-sol` with reasoning effort `medium`.

A deterministic promotion runs direct code checks with zero executor and judge sessions. It can prove the runner and documentation contracts but cannot prove that a future agent will follow prose guidance.

## Existing Context

The repository starts this direction change at commit `1f8ba7554ef10f866f5d23fdadef9793be6f1641`. Tracked files are clean; `_temporary/`, evaluation evidence from earlier attempts, and Python caches are already untracked and must be preserved.

The canonical runner emits `economic_runtime`, validates policy version 1, binds the object into `evaluation_fingerprint`, and protects it through the stable plan field list. The plan schema requires it. The skill and public guides recommend Luna medium for some executors and Terra medium for judges.

Two earlier Sol based promotion attempts consumed 15 sessions in total. The first failed because the evaluation prompt left output placement ambiguous. The second passed all model judged gates reached but stopped when `cost-efficient-runtime-contract` still asserted policy version 1. Those attempts do not justify more model consumption for this deterministic removal.

## Desired End State

Plans do not emit or accept `economic_runtime`, and the evaluation fingerprint no longer includes it. `runtime`, runtime inheritance, explicit promotion blockers, session budgeting, and runtime fingerprints remain unchanged.

`AGENTS.md`, the skill, its contract, and public guides recommend Sol medium for executor and judge. They state that this is guidance rather than a runner default, that model backed promotions still require explicit CLI values and cost authorization, and that static or deterministic work uses zero sessions.

The behavioral `economic-runtime-guidance` case and its dedicated unit tests no longer exist. The deterministic `cost-efficient-runtime-contract` proves that diagnostic and promotion plans omit the retired field.

## Milestones

### Milestone 1 - Isolate and specify removal

#### Goal

Freeze the canonical baseline, create an isolated candidate, and change the deterministic contract before behavior.

#### Changes

- Create immutable baseline and candidate copies under one new `/tmp` directory.
- Change the candidate `cost-efficient-runtime-contract` to reject plans containing `economic_runtime`.
- Remove the obsolete behavioral case from the candidate suite.

#### Validation

- Command: run the changed deterministic contract against the frozen baseline.
- Expected result: exit code 1 because the baseline still emits `economic_runtime`.

#### Acceptance Criteria

- The baseline remains byte for byte unchanged.
- RED is mechanical and consumes zero model sessions.

### Milestone 2 - Remove the runner contract

#### Goal

Make the candidate produce the smaller public plan contract without changing explicit runtime behavior.

#### Changes

- Remove advisory functions, warnings, plan serialization, validation, fingerprint input, and stable field coupling.
- Remove the field and definitions from `references/eval-plan.schema.json`.
- Remove dedicated advisory tests and add focused absence and schema coverage where needed.
- Update the candidate skill and normative evaluation contract.

#### Validation

- Command: `python3 -m unittest discover -s scripts/tests -v` from the candidate.
- Expected result: all tests pass.
- Command: `python3 /home/renanfranca/.codex/skills/.system/skill-creator/scripts/quick_validate.py <candidate>`.
- Expected result: skill structure is valid.

#### Acceptance Criteria

- A generated plan validates without `economic_runtime`.
- Explicit runtime, inherited judge runtime, blockers, budgets, and fingerprints retain their existing behavior.

### Milestone 3 - Prove deterministic promotion

#### Goal

Obtain RED and three stable GREEN observations without real model sessions.

#### Changes

- Run candidate `validate-change` with deterministic impact and an explicit `/tmp` report directory.

#### Validation

- Command: candidate runner `validate-change --impact deterministic --case cost-efficient-runtime-contract`.
- Expected result: baseline RED, candidate GREEN three times, `PASS`, and `model_sessions.total` equal to zero.

#### Acceptance Criteria

- No executor or judge process runs.
- The report is stored only under `/tmp`.

### Milestone 4 - Apply and document

#### Goal

Apply the reviewed candidate patch to the canonical skill and align repository instructions.

#### Changes

- Apply only the candidate diff to canonical `develop-skill-with-evals`.
- Update `AGENTS.md`, `EVALUATIONS.md`, and `CODEX_CLI.md`.
- Preserve historical and propagation uses of other model identifiers.

#### Validation

- Command: canonical unit tests, quick validation, JSON schema validation, focused deterministic case, repository searches, `git diff --check`, and scoped diff review.
- Expected result: every check passes and normative guidance recommends only Sol medium.

#### Acceptance Criteria

- No unrelated tracked or untracked file changes.
- No stage, commit, push, publication, archive rebuild, or model session.

## Progress

- [x] Direction changed from economic recommendation version 2 to complete field removal
- [x] Current HEAD and worktree recorded
- [x] Baseline and candidate frozen at `/tmp/remove-economic-runtime.n7Eyj7`
- [x] Deterministic RED observed
- [x] Candidate implementation complete
- [x] Candidate unit and structural checks pass
- [x] Zero session deterministic promotion passes
- [x] Canonical patch applied
- [x] Repository documentation aligned
- [x] Final scope audit passes

## Decisions

- Decision: Remove `economic_runtime` without a replacement field.
  Rationale: It is advisory only and does not influence the subprocess runtime, so retaining or renaming it would preserve the same ambiguity.
  Date/Author: 2026-07-28 / Codex

- Decision: Recommend Sol medium for executor and judge in repository and skill instructions.
  Rationale: This is the user's explicit preference after repeated Terra judge friction and cost. It remains guidance rather than hidden runner behavior.
  Date/Author: 2026-07-28 / Codex

- Decision: Do not add a new plan version solely for this removal.
  Rationale: The current plan object has no public version field. Adding one would expand the requested change rather than merely retire the advisory property.
  Date/Author: 2026-07-28 / Codex

- Decision: Use deterministic evidence only and make no semantic promotion claim.
  Rationale: The removed behavior and JSON contract are fully code observable, and the user explicitly rejected further model cost.
  Date/Author: 2026-07-28 / Codex

## Risks and Mitigations

- Risk: A consumer still expects `economic_runtime`.
  Mitigation: Treat removal as an intentional public contract change, update the schema and every normative consumer, and search the repository for stale reads.

- Risk: Removing the field weakens stale approval protection.
  Mitigation: Preserve source, case, manifest, runtime, and evaluation fingerprints; verify changed candidate sources still change the evaluation fingerprint.

- Risk: Documentation implies the runner automatically selects Sol.
  Mitigation: State consistently that promotion requires explicit CLI runtime and that the runner does not read `config.toml`.

- Risk: Literal model replacement corrupts history or propagation tests.
  Mitigation: Review each remaining Luna and Terra occurrence and retain historical facts, pricing data, and deliberate override tests.

## Validation Strategy

1. Prove focused RED against the frozen baseline.
2. Run candidate unit tests and schema checks.
3. Run the integrated deterministic promotion with zero sessions.
4. Apply the reviewed patch and update canonical documentation.
5. Repeat canonical checks, search for stale normative guidance, and inspect the complete diff.

## Rollout and Recovery

There is no deployment or publication. Before canonical application, recovery is deletion of the isolated `/tmp` candidate. After application, recovery is a file scoped revert of the runner, schema, tests, skill guidance, and repository documentation. Historical evaluation evidence remains untouched.

## Lessons Learned

- An advisory field is not a runtime control. The runner only executes explicit CLI values or compatible configured fallback behavior in exploratory commands.
- Four earlier Terra judge operations were inconclusive in the documentation campaign, but there was no controlled causal comparison with Sol on identical inputs.
- The first Sol promotion attempt exposed an ambiguous fixture path. The second consumed 13 sessions and exposed a deterministic contract that had been misclassified as regression. Neither failure requires another model backed run to remove the advisory field.
- The correct evidence boundary is mechanical: prove the old field is rejected, the new field is absent, explicit runtime behavior remains stable, and the repository text states the intended recommendation.
- The focused deterministic promotion passed with baseline RED, three candidate GREEN results, evaluation fingerprint `2661ade4ed3d399facb31368f3e1336217561df40242a46c2dc7a0a78c2ba286`, and zero executor or judge sessions.
- The canonical skill passed all 82 remaining unit tests, `quick_validate.py`, JSON Schema validation, Markdown link and anchor validation, candidate equality, and `git diff --check`.
