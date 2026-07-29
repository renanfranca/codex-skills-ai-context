# Generalize ExecPlan TDD

This ExecPlan is a living document. Keep `Progress`, `Decisions`, `Risks and Mitigations`, `Documentation Impact`, and `Lessons Learned` up to date as work advances.

## Purpose / Big Picture

Create a reusable `execplan-tdd` skill for repository guided software changes that combine a living ExecPlan, behavior focused TDD, public journey validation, design review, documentation reconciliation, and final validation. Apply that workflow to every code change under `website/` while preserving `develop-skill-with-evals` as the exclusive workflow for skill development and leaving `seed4j-execplan-tdd` behavior unchanged.

The result is observable when Codex can invoke `$execplan-tdd`, the public website lists the new skill, repository instructions route website code changes through the new workflow, and isolated evaluations prove the workflow follows a complete repository profile and stops before editing when that profile is incomplete.

## Scope

In scope:

- Create the public `execplan-tdd` skill, interface metadata, and isolated evaluation suite.
- Extend `implement-execplan` with a required `Documentation Impact` section and corresponding milestone and final validation obligations.
- Add repository and website instructions for audit memory and website code changes.
- Update repository documentation, ignore rules, website catalog expectations, and audit memory documentation.
- Prove the changed skill behavior through baseline RED and three stable candidate GREEN runs, with cross cutting regression for the new central workflow.
- Run structural, deterministic, website, and final diff validation.

Out of scope:

- Change the behavior of `seed4j-execplan-tdd`.
- Reorganize existing files under the audit repository's `done/` directory.
- Commit, push, publish, deploy, clone automatically, or clean unrelated worktree files.
- Use `execplan-tdd` for documentation only tasks.
- Change existing evaluation runner APIs or schemas.

## Definitions

An ExecPlan is a self contained living Markdown plan that records implementation state, decisions, risks, documentation impact, validation evidence, recovery guidance, and lessons.

The audit memory is the separately versioned clone at `_temporary/codex-skills-ai-context`, with expected `origin` `https://github.com/renanfranca/codex-skills-ai-context.git`. It preserves useful project history but never replaces a self contained ExecPlan.

A repository workflow profile is the set of instructions that names an ExecPlan destination, relevant test suite, public checkpoint, final validation commands, and canonical documentation sources.

A public checkpoint exercises behavior through a stable user visible or consumer facing path rather than through production implementation details.

Baseline RED means an immutable preimplementation skill snapshot fails the new behavioral contract. Candidate GREEN means the implemented skill passes it. Three matching candidate passes establish the required stability evidence.

## Existing Context

The repository stores reusable skills as top level directories with `SKILL.md` and optional `agents/openai.yaml`, references, scripts, and evaluations. `develop-skill-with-evals/SKILL.md` governs all skill changes and requires `skill-creator`, isolated fixtures, immutable baselines, proportional evaluation gates, and fresh agent validation.

`implement-execplan/SKILL.md` currently requires thirteen plan sections but has no explicit documentation impact contract. `seed4j-execplan-tdd/SKILL.md` composes ExecPlan, TDD, and design review for Seed4J and must remain unchanged.

The website generator in `website/scripts/generate-content.mjs` discovers active top level skills dynamically. `website/tests/site-content.test.mjs` currently asserts nine active skills. `website/README.md` defines the site validation commands, and `.generated/` is generated content.

The root `AGENTS.md` defines repository development practices but does not yet define audit memory. No `website/AGENTS.md` currently exists. The root `.gitignore` ignores `.idea` and `.system`, but not `_temporary/`.

The audit memory clone exists at the required path, is clean, and its `origin` matches the expected GitHub URL.

## Desired End State

- `execplan-tdd/SKILL.md` triggers for code changes in repositories that provide a complete workflow profile, loads the three foundation skills in order, preserves a live plan, enforces the relevant suite and public checkpoint before design review, reconciles documentation, and runs final repository validation.
- The skill stops without editing when any required profile field is absent and does not trigger for documentation only work.
- `implement-execplan/SKILL.md` requires `Documentation Impact`, incorporates it into every milestone, and verifies documentation reconciliation during final validation.
- Root and website instructions make audit memory and website workflow requirements explicit without creating an automatic clone, fallback, commit, or push path.
- The audit repository README explains its purpose and forbidden sensitive content.
- The public website catalog contains ten active skills, including `execplan-tdd`.
- New semantic cases prove the intended behavior. Existing cases remain green under the cross cutting regression policy.

## Milestones

### Milestone 1 - Freeze baselines and add failing contracts

#### Goal

Create immutable baseline snapshots and isolated cases that fail before the behavior is implemented.

#### Changes

- Initialize `execplan-tdd` with `.system/skill-creator/scripts/init_skill.py`.
- Copy the untouched scaffold to a temporary immutable baseline.
- Copy the current `implement-execplan` skill to a temporary immutable baseline.
- Add three `execplan-tdd` cases: complete repository profile, incomplete profile gate, and documentation only nonselection.
- Add one `implement-execplan` case covering documentation updates and explicit no change justification.

#### Validation

- Command: `python3 .system/skill-creator/scripts/quick_validate.py ./execplan-tdd`
- Expected result: the untouched scaffold is structurally valid.
- Command: run evaluation planning and the authorized baseline gates described by `develop-skill-with-evals`.
- Expected result: affected baseline cases report valid `FAIL`, not `PASS`, `ERROR`, or `INCONCLUSIVE`.

#### Acceptance Criteria

- The new contracts are isolated and contain no hidden oracle or judge criteria in public fixtures.
- Baselines are immutable and stored outside the source tree.
- Model session count is planned and separately authorized before any model backed execution.

### Milestone 2 - Implement workflow and documentation contracts

#### Goal

Implement the new generic workflow and extend the ExecPlan contract without changing Seed4J behavior.

#### Changes

- Replace the scaffold `execplan-tdd/SKILL.md` with concise ordered gates.
- Generate matching `execplan-tdd/agents/openai.yaml`.
- Add `Documentation Impact` requirements and skeleton content to `implement-execplan/SKILL.md`.
- Regenerate `implement-execplan/agents/openai.yaml` only if its existing interface becomes stale.
- Leave `seed4j-execplan-tdd/**` byte unchanged.

#### Validation

- Command: `python3 .system/skill-creator/scripts/quick_validate.py ./execplan-tdd`
- Command: `python3 .system/skill-creator/scripts/quick_validate.py ./implement-execplan`
- Expected result: both skills are structurally valid and their metadata matches their instructions.

#### Acceptance Criteria

- The workflow requires all repository profile fields, stops before editing when incomplete, and has no documentation only trigger.
- The workflow loads `implement-execplan`, then `tdd-behavior-autonomous-quiet`, then `refactor-design` at the required gates.
- Missing behavior found in design review returns to TDD.
- Documentation is updated or an explicit no change justification is recorded.

### Milestone 3 - Persist repository profiles and public documentation

#### Goal

Make audit memory and website workflow requirements discoverable and durable.

#### Changes

- Update root `AGENTS.md` with audit clone checks, naming, self containment, safety prohibitions, and nested website instruction routing.
- Add `website/AGENTS.md` with the complete `execplan-tdd` workflow profile.
- Update root `README.md` with audit clone setup, website launch guidance, the new skill, and Seed4J migration note.
- Add `/_temporary/` to `.gitignore`.
- Add `README.md` to the audit memory repository with purpose, content boundaries, naming, and forbidden data.
- Update the website catalog test to expect ten active skills and require `execplan-tdd`.

#### Validation

- Command: `npm test` from `website/`.
- Expected result: content and hook tests pass and the generated catalog includes `execplan-tdd`.

#### Acceptance Criteria

- Website code contributors can discover every required workflow profile field from `website/AGENTS.md`.
- No instruction authorizes automatic cloning, fallback paths, commits, or pushes.
- `.generated/` is explicitly treated as disposable and must not be edited.

### Milestone 4 - Evaluate, forward test, and validate

#### Goal

Produce stable behavior evidence and complete repository validation.

#### Changes

- Run the cross cutting promotion gate for `execplan-tdd`.
- Run the scoped promotion gate for `implement-execplan`.
- Forward test `execplan-tdd` with a fresh agent that receives only the candidate skill and a realistic website task.
- Update this plan with results, decisions, risks, documentation impact, and lessons.

#### Validation

- Command: `python3 -m unittest discover -s develop-skill-with-evals/scripts/tests -v`
- Command: `python3 .system/skill-creator/scripts/quick_validate.py ./execplan-tdd`
- Command: `python3 .system/skill-creator/scripts/quick_validate.py ./implement-execplan`
- Command: `npm test` from `website/`
- Command: `npm run prettier:check` from `website/`
- Command: `npm run build` from `website/`
- Command: `npm run test:e2e` from `website/`
- Command: `git diff --check` in the skills repository
- Command: `git diff --check` in `_temporary/codex-skills-ai-context`
- Expected result: every command exits zero.

#### Acceptance Criteria

- Each affected baseline is valid RED.
- Each affected candidate has three stable GREEN results.
- Every remaining `execplan-tdd` suite case passes once between GREEN 1 and GREEN 2.
- Fresh agent behavior demonstrates the workflow without receiving expected output or diagnosis.
- No unrelated worktree change is overwritten, staged, committed, pushed, or removed.

## Progress

- [x] Verified the audit memory clone path, clean state, and expected `origin`.
- [x] Created this ExecPlan before implementation.
- [x] Milestone 1 started.
- [ ] Milestone 1 completed.
- [x] Milestone 2 started.
- [x] Milestone 2 completed.
- [x] Milestone 3 started.
- [x] Milestone 3 completed.
- [x] Milestone 4 started.
- [ ] Milestone 4 completed.

## Decisions

- Decision: Classify creation of `execplan-tdd` as cross cutting because it introduces a central orchestration and selection workflow.
  Rationale: Triggering, safety gates, and composition of shared skills can affect behavior beyond the three directly specified cases.
  Date/Author: 2026-07-29 / Codex

- Decision: Classify the `implement-execplan` documentation contract change as scoped semantic behavior.
  Rationale: The affected behavior is bounded by a focused case about documentation impact and no change justification.
  Date/Author: 2026-07-29 / Codex

- Decision: Preserve `seed4j-execplan-tdd` byte for byte in this delivery.
  Rationale: Its removal is intentionally deferred until the Seed4J repository adopts the generic skill and supplies its own persistent profile.
  Date/Author: 2026-07-29 / Codex

- Decision: Select `repository-profile-implementation` and `incomplete-profile-gate` as affected cross cutting cases, with `documentation-only-boundary` as the remaining suite regression.
  Rationale: The first two cases directly observe the new workflow and safety gate, while the third protects the selection boundary without consuming an additional affected repetition.
  Date/Author: 2026-07-29 / Codex

- Decision: Make no post GREEN refactor to the website catalog test.
  Rationale: The change updates one existing public catalog count and adds a direct inclusion assertion over the same generated model. The design review found no material structural risk or duplicated policy.
  Date/Author: 2026-07-29 / Codex

## Risks and Mitigations

- Risk: Evaluation prompts leak expected behavior or hidden judge criteria.
  Mitigation: Keep public prompts task focused, store only minimal fixtures, and place complete bounded checks under hidden `oracle/` directories where possible.

- Risk: Model backed evaluation consumes unapproved sessions.
  Mitigation: Run side effect free `plan` first, report the maximum session count, and stop until the user separately authorizes that cost.

- Risk: Repository instructions conflict with the generic skill.
  Mitigation: Treat the repository profile as authoritative for commands and sources while keeping the skill responsible for ordering and gates.

- Risk: Generated website files or unrelated caches enter the change.
  Mitigation: Never edit `.generated/`, preserve existing untracked caches, inspect status and diff before final handoff, and add only the requested `_temporary/` ignore.

- Risk: The audit memory becomes mistaken for complete context.
  Mitigation: Require every ExecPlan to remain self contained and describe historical memory as complementary only.

- Risk: The untouched generated scaffold is not structurally valid because its placeholder description parses as a YAML list.
  Mitigation: Preserve it as immutable historical input, use a separately immutable baseline with only the placeholder description normalized to a valid string, and require semantic RED observations to fail on the behavior contract rather than infrastructure.

- Risk: A yielded orchestration wrapper can hide a still running nested evaluation process from host process inspection.
  Mitigation: Launch every remaining long running promotion directly through the unified command tool, retain its explicit session ID, and follow only that session to a terminal result.

## Validation Strategy

First validate structural skill and case contracts. Then demonstrate baseline RED and candidate GREEN under the evaluation runner's proportional gate, only after session cost authorization. Run deterministic runner tests, website unit tests, formatting, production build, and browser journeys. Finish with diff checks in both repositories and a fresh agent forward test using only the candidate skill and realistic task.

Completed deterministic evidence:

- `quick_validate.py` passes for `execplan-tdd` and `implement-execplan`.
- The evaluation planner validates all four cases and reports 18 maximum model sessions for the cross cutting `execplan-tdd` promotion and 8 for the scoped `implement-execplan` promotion.
- `python3 -m unittest discover -s develop-skill-with-evals/scripts/tests -v` passes 82 tests.
- The final website sequence passes: 7 unit tests, Prettier, production build with 10 skills and 44 reports, and 12 desktop and mobile Playwright journeys.
- `git diff --check` passes in both repositories.
- `codex doctor --json` reports `overallStatus: ok`.

Pending authorized model evidence:

- valid affected baseline RED;
- three stable affected candidate GREEN results and the remaining suite regression;
- fresh agent forward test.

Model execution evidence and correction:

- Two identical `execplan-tdd` promotions were inadvertently active concurrently because the first long running command returned control through an orchestration wrapper while its subprocess continued in a separate process namespace. Host process inspection did not reveal that subprocess, and a direct replacement was started.
- Both operations stopped at candidate GREEN 1 with `FAIL` before any judge execution. Each consumed three executor sessions, for six sessions total.
- Retained reports:
  - `evaluation-reports/execplan-tdd/operations/20260729T165358.661874Z-f0f172c87a08/report.json`
  - `evaluation-reports/execplan-tdd/operations/20260729T165518.231174Z-f03918253da4/report.json`
- The failures exposed evaluation contract defects: the incomplete profile case matched harness files with `*`, the documentation oracle rejected a valid concrete no change justification, and the invalid untouched scaffold allowed the globally installed candidate to contaminate baseline behavior.
- The cases now exclude harness artifacts through a hidden no edits oracle, accept bounded lexical justification in `Documentation Impact`, and use a structurally valid behaviorless baseline whose only scaffold normalization is a valid description string.
- The corrected documentation oracle passes the retained real candidate artifact, the no edits oracle passes the untouched fixture, and the normalized baseline passes structural validation.
- A fresh plan requires at most 18 additional sessions for `execplan-tdd`; `implement-execplan` still requires at most 8, and the forward test requires 1. No additional model work may start without fresh authorization for those 27 sessions.

Deterministic readiness after the corrections:

- Rebuilt the canonical evaluation archive and validated 46 reports plus its configured comparison.
- Replaced two brittle website assertions that fixed the archive at 44 operations. Unit and E2E expectations now derive the public count from canonical `evaluation-reports/manifest.json`.
- Confirmed the behavior RED before each test correction: the unit suite first failed on `44 archived operations`, then the public checkpoint failed on the same stale desktop and mobile expectation.
- Repeated the complete website validation in order: 7 unit tests, Prettier, production build with 10 skills and 46 reports, and 12 Playwright journeys all pass.
- Repeated all 82 deterministic runner tests, structural validation for both candidates and the normalized baseline, archive validation, and both repository diff checks.
- Exercised each corrected hidden oracle in both directions: the valid retained artifact passes, while fixtures missing the plan or containing edits fail for the expected reason.
- Confirmed unchanged promotion fingerprints:
  - `execplan-tdd`: `60211489ed4026a4f951cdf58308bb95c955e4991324f76ba97ccdf64546a87f`
  - `implement-execplan`: `ddfb5ddf9bd437d7e2462567cf586b8a820bb3a7b059a1977e250192eee7b134`
- Confirmed `seed4j-execplan-tdd` retains its original two SHA-256 hashes.
- Observed that the core implementation was incorporated externally in commit `78edb38` while this preparation was in progress. No commit, staging, reset, or restoration was performed by this execution.

## Documentation Impact

The change intentionally updates root contribution instructions, the root skill catalog and setup guide, website specific workflow instructions, website catalog assertions, and the audit memory repository README. `website/README.md` remains accurate because it already declares the same four final commands and generated content boundary. `website/content-config.json` requires no change because active top level skills are discovered dynamically and the file only declares disabled compatibility skills. No generated website content is edited directly because it is a disposable projection.

Every milestone must either list the canonical documentation it changes or record why no documentation change is required. Final validation must confirm the changed docs match the implemented behavior and repository commands.

## Rollout and Recovery

No deployment or publication is part of this work. Rollout consists of reviewing and later committing the two repository diffs in separate repositories. Recovery is a normal version control revert of the eventual commits; until then, all source changes remain visible unstaged worktree edits. The legacy Seed4J workflow remains available throughout.

## Lessons Learned

- The required audit clone was already present, clean, and connected to the expected remote, so no clone or fallback action was needed.
- The website catalog discovers top level skills dynamically; only its explicit active skill count and inclusion assertion require a code test change.
- The skill initializer currently produces a placeholder description that the structural validator reads as a list, so an untouched new scaffold cannot satisfy `quick_validate.py` until its frontmatter is implemented.
- The website catalog test produced the expected RED `10 !== 9`; after the focused assertion update, all seven unit tests and twelve desktop and mobile public journeys passed.
- The complete deterministic runner suite passes 82 tests, and the Codex preflight reports a healthy authenticated runtime before any nested model execution.
- Long running nested commands can outlive an orchestration wrapper even when its outer cell reports completion; direct unified sessions are required to avoid accidental concurrent campaigns.
- Archive size is live evidence, not a stable literal. Website tests must compare the rendered count with the canonical manifest instead of fixing a historical number.
