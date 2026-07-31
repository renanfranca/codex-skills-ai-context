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
- [x] Milestone 1 completed.
- [x] Milestone 2 started.
- [x] Milestone 2 completed.
- [x] Milestone 3 started.
- [x] Milestone 3 completed.
- [x] Milestone 4 started.
- [x] Milestone 4 completed.
- [x] Diagnosed duplicate global and repository scoped target skills without a model.
- [x] Chose complete hidden oracles with no semantic judges for the revised promotion.
- [x] Added deterministic target skill isolation to the runner.
- [x] Completed and regression tested all four hidden oracles.
- [x] Replanned the two promotions with an eleven session cumulative maximum.
- [x] Stopped the isolated promotion after one `INVALID_RED` baseline session.
- [x] Replanned promotion around a discriminating RED and obtained fresh cost authorization.
- [x] Promoted `execplan-tdd` with valid RED, three stable GREEN results, and both regressions green.
- [x] Stopped `implement-execplan` promotion after an overstrict lexical oracle rejected a valid justification.
- [x] Added deterministic RED and GREEN coverage for concrete unchanged documentation justifications.
- [x] Obtained fresh authorization and stopped the corrected promotion after another overstrict cross section filename requirement.
- [x] Added deterministic RED and GREEN coverage for cross referenced canonical documentation validation.
- [x] Obtained fresh authorization and promoted corrected `implement-execplan`.
- [x] Completed the fresh agent forward test in one authorized session.
- [x] Observed the final website RED caused by the newly archived `implement-execplan` evidence.
- [x] Made the no evidence public journey independent of a skill name and restored its suite and public checkpoint.
- [x] Completed the post GREEN design review for the final website test correction with no refactor required.
- [x] Completed the repeated final validation and repository integrity checks.

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

- Decision: Supersede the original affected case selection after `repository-profile-implementation` produced `INVALID_RED`. Select only `incomplete-profile-gate` as affected, with `repository-profile-implementation` and `documentation-only-boundary` as cross cutting regressions.
  Rationale: The complete profile fixture itself states nearly the full workflow, so a behaviorless scaffold can satisfy that implementation contract using repository instructions and general capabilities. The incomplete profile case uniquely observes the candidate skill's stop without editing gate, while the implementation case remains valuable as a candidate regression.
  Date/Author: 2026-07-29 / Codex

- Decision: Make no post GREEN refactor to the website catalog test.
  Rationale: The change updates one existing public catalog count and adds a direct inclusion assertion over the same generated model. The design review found no material structural risk or duplicated policy.
  Date/Author: 2026-07-29 / Codex

- Decision: Disable the globally installed copy of the target skill inside every semantic executor while preserving the repository scoped evaluation copy and all dependency skills.
  Rationale: `codex debug prompt-input` proved that baseline executions received both the placeholder and the completed global `execplan-tdd`. A path scoped `skills.config` override removes only the global target and leaves the local baseline visible, so RED and GREEN can be compared without copying credentials or creating another `CODEX_HOME`.
  Date/Author: 2026-07-29 / Codex

- Decision: Disable semantic judges for all four new cases after completing their hidden oracles.
  Rationale: Filesystem state, structured executor response, public commands, and bounded lexical concepts completely cover the required observable contracts. A judge would double session cost while only evaluating self reported chronology that cannot be proven from the final workspace.
  Date/Author: 2026-07-29 / Codex

- Decision: Supersede `incomplete-profile-gate` as the affected RED case after isolated execution.
  Rationale: The untouched baseline scaffold explicitly identifies itself as unimplemented and contains visible `TODO` placeholders. A prudent baseline agent therefore stops without editing even though the generic workflow gate is absent. The case remains a valid candidate regression, but it cannot demonstrate behavioral absence in the scaffold.
  Date/Author: 2026-07-29 / Codex

- Decision: Observe implicit selection in `repository-profile-implementation` and use it as the affected RED.
  Rationale: The public task now requests only the code behavior, while the fixture's repository profile requires `$execplan-tdd`. The candidate description advertises the complete code change workflow and the scaffold description advertises only an unimplemented placeholder. This isolates a public trigger contract instead of asking both agents explicitly to use a skill. The hidden oracle continues to require the same final behavior and workflow evidence.
  Date/Author: 2026-07-29 / Codex

- Decision: Select the first rendered skill card with no archived evidence in the public journey instead of naming `implement-execplan`.
  Rationale: The behavior under test is the no evidence state, while whether one particular skill has reports is live archive data. The completed promotion legitimately moved `implement-execplan` out of that state.
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

- Risk: A complete repository profile can make a workflow implementation case pass without the candidate skill and invalidate RED.
  Mitigation: Use the incomplete profile stop gate as the sole affected case and retain the complete implementation case as cross cutting candidate regression. Require a new side effect free plan and fresh cost authorization before another promotion.

- Risk: A global target skill with the same frontmatter name contaminates baseline and candidate prompts.
  Mitigation: Add the exact global target path as a disabled `skills.config` entry in the executor command, prove command construction in unit tests, and inspect a materialized prompt without a model before any promotion.

- Risk: Semantic judges add stochastic cost without observing more than the hidden oracle.
  Mitigation: Make every new oracle complete over final files and structured response, disable all four judges, and retain three executor GREEN repetitions only for the affected model behavior.

- Risk: The scaffold's explicit `unimplemented` and `TODO` text makes a missing profile stop indistinguishable from ordinary agent caution.
  Mitigation: Keep the stop case as candidate regression rather than affected RED. Select the complete profile implementation case for RED only after target isolation removes the globally installed candidate from the baseline prompt.

- Risk: A fully specified repository profile lets general agent behavior imitate the candidate workflow.
  Mitigation: Remove the artificial explicit invocation from the implementation prompt and declare the case implicit. Confirm without a model that the baseline sees exactly the placeholder description, the candidate sees exactly the complete description, all three dependency skills remain visible, and the runner adds no explicit target instruction.

- Risk: A lexical documentation oracle rejects a concrete justification because of ordinary inflection or synonymous no edit wording.
  Mitigation: Accept a bounded set covering `remain accurate`, `remains accurate`, `unchanged`, and `without edits`, while retaining a negative test that rejects plans which merely name the canonical sources.

- Risk: The oracle requires canonical filenames to be repeated in `Validation Strategy` even when `Documentation Impact` maps those filenames and the strategy explicitly validates canonical documentation.
  Mitigation: Accept either both literal source names or a controlled cross reference that names `config.json`, documentation, and an inspection, validation, reconciliation, or comparison action. Retain a negative test that rejects validation limited to unit tests.

- Risk: A public journey names a skill whose archive state changes when a promotion report is retained.
  Mitigation: Select by the user visible `No archived evidence` state and derive the expected detail heading from the selected card.

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

Latest promotion result:

- The authorized direct `execplan-tdd` promotion stopped after its first baseline execution with `INVALID_RED`; no candidate, regression, or stability execution started.
- The operation consumed one executor and one judge session, recorded as 2 sessions in `/tmp/generalize-execplan-tdd-60211489-campaign.json`.
- Retained report: `evaluation-reports/execplan-tdd/operations/20260729T172226.771985Z-385bfa7090aa/report.json`.
- Root cause: `repository-profile-implementation` is not a discriminating RED because its `AGENTS.md` fixture gives a behaviorless scaffold enough workflow detail to complete the task.
- The corrected selection uses only `incomplete-profile-gate` as affected and keeps the other two cases as candidate regressions. Its side effect free plan has evaluation fingerprint `c64406c1d261114aa286301dc4ab23785a1c286b690fcb54c2b452a5c2e83e10` and requires at most 12 sessions.
- No second promotion, `implement-execplan` promotion, or forward test started after the blocking result. A new model backed operation requires fresh cost authorization.
- After recording the failed operation, archive rebuild and validation pass with 47 reports and one comparison; both skill structural checks, all 82 runner tests, the complete four command website sequence, and both repository diff checks pass.

Second corrected promotion result:

- The promotion with evaluation fingerprint `c64406c1d261114aa286301dc4ab23785a1c286b690fcb54c2b452a5c2e83e10` produced the expected semantic baseline RED and candidate stop behavior, but both executions were marked `FAIL` by the hidden oracle before judging.
- The oracle incorrectly treated `.git/**` metadata initialized by the runner as task created files. The operation stopped after two executors, consumed no judge sessions, and retained `evaluation-reports/execplan-tdd/operations/20260729T173437.653533Z-bda290271677/report.json`.
- The oracle now ignores only `.git` metadata in addition to existing harness exclusions. It passes both retained real workspaces and still rejects an edited `pricing.py`.
- Added two permanent deterministic regression tests covering runner initialized `.git` metadata and project file mutation. All 84 runner tests pass.
- The side effect free promotion plan now has evaluation fingerprint `d6edd1fb33df68ee849db8e652500080641703bb6391d4d938025c94cd1da783` and still requires at most 12 sessions.
- Archive validation passes with 48 reports and one comparison. Both structural checks, the complete website sequence with 48 rendered reports, and both repository diff checks pass.

Deterministic isolation and cost correction:

- `codex debug prompt-input` proved the original baseline prompt contained both the placeholder and the completed global `execplan-tdd`.
- A path scoped `skills.config` override now disables the global target only for semantic executors. Prompt inspection without a model shows exactly one target in each `execplan-tdd` baseline and candidate workspace, while `implement-execplan`, `tdd-behavior-autonomous-quiet`, and `refactor-design` remain visible.
- The same inspection shows exactly one `implement-execplan` target in its baseline and candidate workspaces.
- Added `global-target-skill-isolation` as a deterministic self evaluation. Against the immutable `HEAD` baseline it produces valid RED, followed by three candidate GREEN results with zero model sessions. Evaluation fingerprint: `f5ff8bdaac2f50ba873a61e29daf4aad5118e28b33283953581e658a6a6ae3e5`.
- All four new semantic cases now use complete hidden oracles and disabled judges. The incomplete profile oracle validates exact unchanged project state plus bounded response concepts; the documentation boundary oracle ignores only harness metadata and rejects any additional plan or project file.
- All 89 deterministic runner tests pass, including positive and negative oracle tests.
- Revised `execplan-tdd` promotion: six executor sessions, zero judges, evaluation fingerprint `26a9fc7898fc1f2ab793cd69cf91652514693078e890abe688feb1cf37e577c2`.
- Revised `implement-execplan` promotion: four executor sessions, zero judges, evaluation fingerprint `c38e72f16e17758808cd2b3aded5693a24e93cc4a1998284f50e22778fbcd833`.
- The two promotions plus one fresh agent forward test require at most eleven new model sessions. No semantic promotion started during this correction.
- Structural validation passes for `develop-skill-with-evals`, `execplan-tdd`, and `implement-execplan`; archive validation passes with 48 reports; the complete website sequence passes with 10 skills, 48 reports, 7 unit tests, and 12 browser journeys.

Isolated promotion result after deterministic readiness:

- `codex doctor --json` reported `overallStatus: ok`, and the new campaign ledger was absent before launch.
- The direct promotion with evaluation fingerprint `26a9fc7898fc1f2ab793cd69cf91652514693078e890abe688feb1cf37e577c2` stopped after the first executor session with `INVALID_RED`; no candidate, regression, stability, or judge execution started.
- The baseline correctly left every project file unchanged and explicitly reported the missing public checkpoint and canonical documentation sources. Its visible scaffold says it is unimplemented and contains `TODO` placeholders, so this outcome does not distinguish the new safety gate.
- The operation consumed one executor session in `/tmp/generalize-execplan-tdd-isolated-26a9fc78-campaign.json` and retained `evaluation-reports/execplan-tdd/operations/20260729T180639.451565Z-5356efabeb4c/report.json`.
- No unchanged retry or second promotion was started. Replanning and fresh cost authorization are required.
- The implementation case now uses implicit selection. A focused manifest test and the full deterministic runner suite pass 90 tests. Structural validation passes for all three changed skills.
- `codex debug prompt-input` confirms without a model that the baseline prompt contains one placeholder target description and no complete target description, while the candidate contains one complete target description and no placeholder. The candidate prompt still contains exactly one description for each required dependency and no runner injected `Use $execplan-tdd` instruction.
- The new side effect free cross cutting plan has evaluation fingerprint `40a179fc33a7c469079b35e343b3cd1cce7f4ecaa3e2b45e4764f34a969bf663`: six executor sessions, zero judges. The previous failed operation consumed one session; completing both promotions requires at most ten additional executor sessions, and the required fresh agent forward test requires one additional session.
- Archive rebuild and validation pass with 49 reports and one comparison. The complete website sequence passes outside the restricted sandbox: 7 unit tests, Prettier, production build with 10 skills and 49 reports, and 12 desktop and mobile Playwright journeys. Both repository diff checks pass.

Authorized promotion evidence:

- `execplan-tdd` promotion `20260729T181620.575049Z-cc3d76bb204a` passed with evaluation fingerprint `40a179fc33a7c469079b35e343b3cd1cce7f4ecaa3e2b45e4764f34a969bf663`: one valid baseline RED, three stable candidate GREEN executions, green incomplete profile and documentation only regressions, six executor sessions, and zero judges.
- `implement-execplan` promotion `20260729T182705.056621Z-0c0e19b3429b` produced valid baseline RED but stopped at candidate GREEN 1 after two executor sessions total. The plan contained `Documentation Impact` and stated that both canonical sources “remain accurate without edits because” their public contracts were unchanged. The oracle rejected only because it recognized `remain accurate`, not `remains accurate`.
- Added a deterministic regression that reproduces the concrete valid wording and a negative control that merely names the documentation. The positive test failed before the oracle correction; both tests now pass, and the retained real candidate artifact passes the corrected oracle.
- All 92 deterministic runner tests and structural checks pass. Archive rebuild and validation pass with 51 reports. The corrected scoped promotion plan has evaluation fingerprint `decb18f9be177c87fe52882f8db9177f18c80aa29cde3ff326ffa7aca40a2eb8`, requires four executor sessions and zero judges, and projects the campaign ledger from nine to thirteen consumed sessions.
- Corrected promotion `20260729T184809.268321Z-a248a1eb23ca` again produced valid baseline RED and stopped at candidate GREEN 1 after two executor sessions. The candidate's `Documentation Impact` explicitly mapped `README.md` and `config.json`; `Validation Strategy` parsed `config.json`, exercised the service with it, and inspected public field occurrences in canonical documentation. The oracle rejected only because `README.md` was not repeated literally in that section.
- Added deterministic positive coverage for this cross referenced validation and a negative control whose strategy runs only unit tests. The positive produced RED before the correction; all four focused oracle tests and the retained real candidate artifact now pass.
- All 94 deterministic runner tests and structural checks pass. Archive rebuild and validation pass with 52 reports. The new scoped plan has evaluation fingerprint `97d8d7ccd4aab87fed09c5cb2df373c46b2fe687c63527892a8ab30f25193970`, requires four executor sessions and zero judges, and projects the campaign ledger from eleven to fifteen consumed sessions.

Completed promotion and forward evidence:

- `implement-execplan` promotion `20260729T190010.008363Z-e8bbde9419ab` passed with evaluation fingerprint `97d8d7ccd4aab87fed09c5cb2df373c46b2fe687c63527892a8ab30f25193970`: valid baseline RED, three stable candidate GREEN executions, four executor sessions, zero judges, and 801,559 total tokens including 641,280 cached input tokens.
- The campaign ledger consumed exactly its cumulative maximum of fifteen executor sessions across the isolated campaign.
- A fresh `gpt-5.6-sol` agent with medium reasoning received only the candidate `execplan-tdd` skill and a realistic website catalog task in `/tmp`. It created a self contained plan in the verified audit worktree, observed behavior RED, delivered conditional summary behavior, ran the public checkpoint, performed post GREEN design review, reconciled all three canonical documentation sources, preserved `.generated/`, and recorded the final command sequence.
- Independent deterministic inspection confirmed the forward fixture's two behavior tests, Prettier check, build, and E2E checkpoint all pass in order.
- Archive rebuild and validation pass with 53 reports and one comparison.
- The first real website final sequence produced 10 passing journeys and two expected failures in the no evidence journey. The retained promotion means `implement-execplan` now has three archived reports, so its card correctly no longer matches `No archived evidence`. This is a stale test data selection, not an application defect or nondeterministic timeout.
- TDD correction changed the E2E journey to select the first public skill card that actually displays `No archived evidence` and derive its expected heading from that card. The relevant suite passed 7 tests and the public checkpoint passed all 12 desktop and mobile journeys.
- Post GREEN design review classified the final test correction as `No action`: it observes one public state and does not duplicate generator policy or introduce mutable state, fragile mapping, or a test only production seam.
- Repeated final validation in the required order passed: `npm test` with 7 tests, `npm run prettier:check`, `npm run build` with 10 skills and 53 reports, and `npm run test:e2e` with 12 journeys.
- Final structural validation passes for `develop-skill-with-evals`, `execplan-tdd`, and `implement-execplan`; all 94 deterministic runner tests pass; archive validation passes with 53 reports and one comparison.
- `git diff --check` passes in both repositories. Final Seed4J compatibility hashes remain `c34c3e9691fcc83449aeea4cba3ff769f42501c1452b94aca3d4560012bed95c` for `SKILL.md` and `2dad86b00eac925304fc852e619d387fa9ec658d33c65bb8be80315b7e754ed1` for `agents/openai.yaml`.
- No files were staged, committed, pushed, published, or deleted. Existing Python caches remain untouched and outside the intended change.

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

The final E2E correction requires no canonical documentation update. It changes only how the existing public “No archived evidence” journey selects whichever current skill is in that state; the public state, configuration, commands, and contributor workflow remain unchanged.

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
- A realistic complete profile case proves candidate capability but not necessarily baseline absence. The RED case must isolate behavior supplied by the candidate skill, such as refusing to edit when the profile is incomplete.
- An explicit placeholder scaffold can independently trigger the same prudent refusal as an incomplete profile gate. A safety behavior is not a usable RED merely because its final outcome is desirable; the baseline must lack the observable behavior for reasons controlled by the fixture.
- Website tests that spawn child processes and the Playwright server can report `EPERM` inside the restricted sandbox before any assertion executes. Their canonical result must come from the repository's approved external permission boundary; build and archive validation alone do not substitute for that run.
- Repository scoped installation does not by itself hide a globally installed skill with the same name. Prompt inspection must prove there is exactly one target skill before any paid evaluation.
- A semantic judge cannot prove that TDD or design review happened in the reported order. Promotion evidence should claim only mechanically observable final behavior and bounded structured evidence.
- A public journey for an archive state must select by that rendered state, not by a skill name whose evidence can legitimately change during the same delivery.
