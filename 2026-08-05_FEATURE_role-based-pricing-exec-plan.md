# Implement role-based pricing snapshots

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, and `Lessons Learned` current while work advances.

## Purpose / Big Picture

Deliver API reference estimates that price executor and judge tokens using each role's executed model. Reports generated before this change must replay byte-for-byte, while new mixed-role reports expose auditable per-role subtotals based on a new dated pricing snapshot.

## Scope

In scope: the evaluation runner's pricing contract, report schema and rendering, report comparison, deterministic tests and self evaluation, pricing snapshot rotation, documentation, replay validation and a fresh-agent test. Out of scope: actual billing, runtime recommendation changes, and changes to archived report JSON, digests, Markdown, or comparisons.

## Definitions

* A role is the executor or judge invocation recorded for one evaluation observation.
* A role estimate is that role's reference-price subtotal and audit metadata.
* A snapshot is an immutable dated API reference pricing file embedded by provenance in new reports.
* Long-context indeterminate means request-scoped long-context pricing cannot be determined from turn-scoped usage.

## Existing Context

`run_skill_evals.py` aggregates role usage before pricing. The pricing function, renderer, comparator, schema, archive validator and tests live in `develop-skill-with-evals`. The 2026-07-26 pricing snapshot and every archived projection are immutable compatibility inputs.

## Desired End State

New reports price each executed role separately, expose ordered `role_estimates`, retain legacy single-role presentation, and use the 2026-08-05 Sol, Terra, and Luna snapshot. Mixed historical reports are compared using reconstructed executor and judge role usage without changing their persisted projections.

## Milestones

### Milestone 1 - Establish contract evidence

#### Goal

Add a fully mechanical self evaluation that rejects the old aggregate pricing behavior and proves role pricing in three stable candidate runs.

#### Changes

- [ ] Add `role-based-pricing-contract` case, minimal fixtures and hidden checker.
- [ ] Register it in the self-evaluation suite.
- [ ] Preserve an immutable baseline and isolated candidate copy.

#### Validation

- [ ] Command: `run_skill_evals.py validate-change ... --impact deterministic --case role-based-pricing-contract`
- [ ] Expected result: valid baseline RED and three stable candidate GREEN runs with zero model sessions.

#### Acceptance Criteria

- [ ] The oracle proves executor and judge use their individual model rates.
- [ ] The candidate's three normalized signatures are identical.

### Milestone 2 - Implement and document the contract

#### Goal

Represent role usage and estimates through calculation, report schema, rendering, comparison, archive validation and guidance.

#### Changes

- [ ] Change runner pricing inputs and aggregation order.
- [ ] Add additive `role_estimates` schema and documentation.
- [ ] Preserve legacy one-role fields and archived rendering.
- [ ] Add the 2026-08-05 snapshot and set archive configuration.

#### Validation

- [ ] Command: focused `unittest` modules and `quick_validate.py`.
- [ ] Expected result: new role cases pass and no old archive projection changes.

#### Acceptance Criteria

- [ ] Mixed and same-model role scenarios produce auditable subtotals.
- [ ] Partial telemetry, no roles, missing models and long context follow the documented aggregate status.

### Milestone 3 - Replays and promotion checks

#### Goal

Verify archived evidence, comparison correction, full deterministic suite, schema validation, and fresh-agent use.

#### Changes

- [ ] Generate fixture reports for role comparison tests.
- [ ] Validate archive, schemas and byte-identical replays.
- [ ] Run a realistic task with a fresh agent given only the candidate skill.

#### Validation

- [ ] Command: `manage_evaluation_archive.py validate --archive evaluation-reports`.
- [ ] Expected result: archive remains valid and generated Markdown matches JSON projections.

#### Acceptance Criteria

- [ ] Three mixed-role fixtures compare with corrected cost.
- [ ] The fresh agent produces a mixed-role report without disclosure of the known defect.

## Progress

- [x] Milestone 1 started
- [x] Milestone 1 completed
- [x] Milestone 2 started
- [x] Milestone 2 completed
- [x] Milestone 3 started
- [x] Milestone 3 completed

## Decisions

- Decision: Treat this as a deterministic, cross-cutting runner change and add a deterministic self evaluation first.
  Rationale: Every requested outcome is mechanically observable, but pricing propagates through several shared contracts.
  Date/Author: 2026-08-05 / Codex

- Decision: Protect the 2026-07-26 snapshot and existing evidence as immutable inputs.
  Rationale: Historical projections must remain reproducible.
  Date/Author: 2026-08-05 / Codex

- Decision: Implement only in `feature/role-based-pricing` at `/tmp/codex-skills-role-based-pricing`.
  Rationale: The canonical source worktree already contained unrelated files and must remain untouched.
  Date/Author: 2026-08-05 / Codex

## Risks and Mitigations

- Risk: An additive contract accidentally changes legacy Markdown.
  Mitigation: replay every archived report and assert byte identity before promotion.
- Risk: Turn-scoped usage is confused with request-scoped long-context pricing.
  Mitigation: preserve indeterminate status and base-rate reference per affected role.
- Risk: Cache writes are falsely treated as cached input.
  Mitigation: retain an explicit pricing limitation because current telemetry cannot identify writes.

## Validation Strategy

1. Add and run focused deterministic pricing and render/comparison tests.
2. Run the deterministic self-evaluation RED/GREEN gate.
3. Run the complete deterministic runner suite, `quick_validate.py`, schema checks and archive validation.
4. Re-render archived reports and compare their bytes with the preserved projections.
5. Forward-test the candidate with a fresh agent on a realistic mixed-role report task.

## Documentation Impact

Update `SKILL.md`, relevant contract references and the permissive report schema to describe role estimates, ordered roles, aggregate semantics and cache-write limitation. `agents/openai.yaml` must remain aligned if its descriptive metadata is affected. The historical snapshot does not change.

## Rollout and Recovery

Roll out by promoting only the reviewed candidate patch after deterministic and forward validation. If a regression is detected, revert the code and configuration changes together; archived reports and the 2026-07-26 snapshot require no recovery because they remain untouched.

## Lessons Learned

- The primary worktree excludes the managed `.system` directory, so structural validation uses the environment-provided `skill-creator` validator.
- The archive validates unchanged reports and comparison projections after the archive configuration moves to the new dated snapshot because every historical report embeds its original snapshot.
- A fresh agent produced a mixed executor and judge report from the candidate without learning the defect or expected cost; the report rendered per-role details and its digest validated.
